---
title: When using JAXB...
tags: [java, JAXB]
image: "/images/JAXB/jaxb.jpg"
bigimg: "/images/JAXB/banner.jpg"
---

Not many examples show this, but how you use JAXB in your application can make a huge difference in the performance (and memory usage).

## The example
In this blog post I'll use an example Object called `Membership` that looks something like this:

![clone1](/images/JAXB/pojo_uml.png)

We will marshal and unmarshal this object to and from XML using JAXB.

## Create the context in a static block (or at least only once)

The biggest mistake I usually see is that the JAXB context gets created on every request:

```java

  public String marshal(Membership membership){
        StringWriter stringWriter = new StringWriter();
        try {
            JAXBContext context = JAXBContext.newInstance(Membership.class);
            Marshaller m = context.createMarshaller();
            m.setProperty(Marshaller.JAXB_FRAGMENT, Boolean.TRUE);
            m.setProperty(Marshaller.JAXB_FORMATTED_OUTPUT, Boolean.TRUE);
            m.marshal(membership, stringWriter);
            String xml = stringWriter.toString();
            stringWriter.close();
            return xml;
        } catch (JAXBException | IOException ex) {
            throw new RuntimeException(ex);
        }
    }

    public Membership unmarshal(String xml) {
        try {
            JAXBContext context = JAXBContext.newInstance(Membership.class);
            Unmarshaller u = context.createUnmarshaller();
            return (Membership)u.unmarshal(new StringReader(xml));
        }catch (JAXBException ex) {
            throw new RuntimeException(ex);
        }
    }

```
(Also see the example code [here](https://github.com/phillip-kruger/jaxb-lib/blob/master/jaxb-batch-example/src/main/java/com/github/phillipkruger/jaxblib/example/util/BadJAXBUtil.java))

The problem here is the `JAXBContext.newInstance` method that creates the context.
The context only changes if the object structure changes, and that only happens on a code change,
so we can safely only do this once, so change this to be created in a static block like this:

```java

    public String marshal(Membership memberships){
        StringWriter stringWriter = new StringWriter();
        try {
            Marshaller m = context.createMarshaller();
            m.setProperty(Marshaller.JAXB_FRAGMENT, Boolean.TRUE);
            m.setProperty(Marshaller.JAXB_FORMATTED_OUTPUT, Boolean.TRUE);
            m.marshal(memberships, stringWriter);
            String xml = stringWriter.toString();
            stringWriter.close();
            return xml;
        } catch (JAXBException | IOException ex) {
            throw new RuntimeException(ex);
        }
    }

    public Membership unmarshal(String xml) {
        try {
            Unmarshaller u = context.createUnmarshaller();
            return (Membership)u.unmarshal(new StringReader(xml));
        }catch (JAXBException ex) {
            throw new RuntimeException(ex);
        }
    }

    private static JAXBContext context;
    static{
        try {
            context = JAXBContext.newInstance(Membership.class);
        } catch (JAXBException ex) {
            throw new RuntimeException(ex);
        }
    }

```
(Also see the example code [here](https://github.com/phillip-kruger/jaxb-lib/blob/master/jaxb-batch-example/src/main/java/com/github/phillipkruger/jaxblib/example/util/GoodJAXBUtil.java))


So lets look at what difference that makes.

### Batch Example.

If we convert 10000 objects to and from XML in a loop (one at a time) these are the results:

```bash
Testing 10000 with Bad util

Marshal took: 10804 ms
Unmarshal took: 13762 ms
```

and then with the static block:

```bash
Testing 10000 with Good util

Marshal took: 90 ms
Unmarshal took: 428 ms
```

That is marshalling 120 times and unmarshalling 32 times faster!!

(Full example [here](https://github.com/phillip-kruger/jaxb-lib/tree/master/jaxb-batch-example))

### Concurrency Example.

Similarly, when doing this with multiple concurrent requests you should see the same results.
So when we deploy this to some server ([thorntail](https://thorntail.io/) in my example), and exposing a REST endpoint to marshal and unmarshal,
we can then use something like [siege](https://www.joedog.org/siege-manual/) to generate concurrent traffic to the server:

Output of the bad example:

```bash
Transactions:                    255 hits
Availability:                 100.00 %
Elapsed time:                   7.91 secs
Data transferred:               0.54 MB
Response time:                  5.13 secs
Transaction rate:              32.24 trans/sec
Throughput:                     0.07 MB/sec
Concurrency:                  165.52
Successful transactions:         255
Failed transactions:               0
Longest transaction:            6.88
Shortest transaction:           3.47
```

Output of the good example:

```bash
Transactions:                    255 hits
Availability:                 100.00 %
Elapsed time:                   1.80 secs
Data transferred:               0.53 MB
Response time:                  0.52 secs
Transaction rate:             141.67 trans/sec
Throughput:                     0.30 MB/sec
Concurrency:                   73.12
Successful transactions:         255
Failed transactions:               0
Longest transaction:            0.78
Shortest transaction:           0.05
```

Note the 'concurrency' value difference
(Concurrency is average number of simultaneous connections, a number which rises as server performance decreases)

(Full example [here](https://github.com/phillip-kruger/jaxb-lib/tree/master/jaxb-concurrency-example))

## When the file is very very big.

If your input file is too big, you might get a `java.lang.OutOfMemoryError` exception.

To make sure that you can handle big files effectively, you can make sure that you are using a SAX Parser when creating the input:

```java
    public Membership unmarshalWithSAX(InputStream xml){
        try {
            InputSource inputSource = new InputSource(xml);
            SAXParserFactory spf = SAXParserFactory.newInstance();
            spf.setNamespaceAware(true);
            spf.setValidating(true);

            SAXParser saxParser = spf.newSAXParser();
            saxParser.setProperty(JAXP_SCHEMA_LANGUAGE, W3C_XML_SCHEMA);

            XMLReader xmlReader = saxParser.getXMLReader();
            SAXSource source = new SAXSource(xmlReader, inputSource);

            Unmarshaller u = context.createUnmarshaller();

            return (Membership)u.unmarshal(source);
        }catch (ParserConfigurationException | SAXException | JAXBException ex) {
            throw new RuntimeException(ex);
        }
  }
  private static final String JAXP_SCHEMA_LANGUAGE = "http://java.sun.com/xml/jaxp/properties/schemaLanguage";
  private static final String W3C_XML_SCHEMA = "http://www.w3.org/2001/XMLSchema";
```

(Full example [here](https://github.com/phillip-kruger/jaxb-lib/tree/master/jaxb-size-example))

## Get it all

You can get all the 'good' in a simple library:

### Using it in your code

(See [https://github.com/phillip-kruger/jaxb-lib](https://github.com/phillip-kruger/jaxb-lib))

```xml
    <dependency>
        <groupId>com.github.phillip-kruger.jaxb-library</groupId>
        <artifactId>jaxb-lib</artifactId>
        <version>1.0.0</version>
    </dependency>
```

#### Marshal

```java
    JaxbUtil jaxbUtil = new JaxbUtil();
    byte[] xml = jaxbUtil.marshal(myJAXBObject);
```

#### Unmarshal

```java
    JaxbUtil jaxbUtil = new JaxbUtil();
    MyJAXBObject myJAXBObject = jaxbUtil.unmarshal(MyJAXBObject.class,xml);
```

#### Getting the XSD for a JAXB Object

```java
    XsdUtil xsdUtil = new XsdUtil();
    String xsd = xsdUtil.getXsd(MyJAXBObject.class);
```
