---
title: Some factory examples.
tags: [Java EE, OpenLiberty, Payara Micro, Wildfly Swarm, CDI, EJB, SPI] 
bigimg: "/images/Some_factory_examples/banner.jpg"
---

Every now and then I find myself scratching though some of my old code to find that example "where I did that factory like thing".

When this happened again last week I decided to just find all examples and create an [example project](https://github.com/phillip-kruger/factories-example) and blog post about it. 

So in this post I:
 
* start with a plain "vanilla" [Java SE](https://en.wikipedia.org/wiki/Java_Platform,_Standard_Edition) factory example
* then one using Java SE [SPI](https://en.wikipedia.org/wiki/Service_provider_interface)
* [CDI](http://cdi-spec.org/) on Java SE 
* CDI on [Java EE](https://en.wikipedia.org/wiki/Java_Platform,_Enterprise_Edition)
* [EJB](https://en.wikipedia.org/wiki/Enterprise_JavaBeans) on Java EE
* Dynamic SPI on Java SE 
* and lastly SPI on Java EE

# The example.

This example app is a very simple "Hello World" that you can pass in a name and there are multiple ways to say hello.

![example_app](https://github.com/phillip-kruger/factories-example/raw/master/HighLevel.png "Example App")

The `Greeting Service` get a instance of the `Greeting Factory`. It can then ask the factory for a `Greeting` (interface) by name, and the factory will return the correct implementation.

There are 3 concrete implementations:

* `English` will greet "Good day <name>."
* `Afrikaans` will greet "Goeie dag <name>." (see [https://www.youtube.com/watch?v=CtxB4sbV0pA])
* `Bugs Bunny` will greet "Eeee, what's up <name> ?" (see [https://www.youtube.com/watch?v=UeVtZjGII-I])

All the [source code](https://github.com/phillip-kruger/factories-example) for this blog is available in Github:

```bash
    git clone https://github.com/phillip-kruger/factories-example
```

The [Greeting](https://github.com/phillip-kruger/factories-example/blob/master/vanilla/src/main/java/com/github/phillipkruger/factory/api/Greeting.java) interface:
```java
    public interface Greeting {
        public String getName();
        public String sayHello(String to);
    }
```

<hl/>

# Vanilla 

This basic Java SE has a main method that allows you to pass in your name and the way(s) you want to be greeted.

The factory is a basic `if-statement` to get the correct implementation:

```java
    public Greeting getGreeting(String name){
        if(name.equalsIgnoreCase("BugsBunny")){
            return new BugsBunny();
        }else if(name.equalsIgnoreCase("Afrikaans")){
            return new Afrikaans();
        }else {
            return new English();
        }
    }
```

An example of a concrete implementation, English:

```java
    public class English implements Greeting {

        @Override
        public String sayHello(String to) {
            return "Good day " + to + ".";
        }

        @Override
        public String getName() {
            return "English";
        }
    }
```

## Run the example:

In the vanilla folder:
```bash
    mvn clean install
```

This will build the project and also run the app. The log will output:

```bash
    SEVERE: 
    Good day Phillip.
    Goeie dag Phillip.
    Eeee, what's up Phillip ?
```

You can also run this outside of maven:

```bash
    java -jar target/vanilla-1.0.0-SNAPSHOT.jar World BugsBunny
    SEVERE: 
    Eeee, what's up World ?
```
## Also see

* [https://alvinalexander.com/java/java-factory-pattern-example]

<hl/>

# Service provider interface (SPI)

The above example means I can very easy add another implementation, and update the if statement to return that implementation when asked.

However, it's that if-statement that we want to improve. We want to get to a point where I can add new implementations without having to modify existing code. 
All I want to do is add the new implementation.

SPI is part of Java SE and is an API that allow you to build plug-able extentions.

## Breaking the application up into modules.

The first thing we are going to do is to [break the application up into modules](https://github.com/phillip-kruger/factories-example/blob/master/spi/pom.xml):

* API - This will contain the Greeting interface (our contact)
* Engine - This will contain the Service and Factory (and the default English implementation)
* Other implementations - All other implementations become their own module (So one for Afrikaans and one for Bugs Bunny etc.)

This already means I can add a new implementation by just creating a new module, not having to touch the code, just update the dependencies: 

```xml
    <dependencies>
        <dependency>
            <groupId>${project.groupId}</groupId>
            <artifactId>spi-api</artifactId>
            <version>${project.version}</version>
        </dependency>
        
        <dependency>
            <groupId>${project.groupId}</groupId>
            <artifactId>spi-impl-afrikaans</artifactId>
            <version>${project.version}</version>
        </dependency>
        
        <dependency>
            <groupId>${project.groupId}</groupId>
            <artifactId>spi-impl-bugsbunny</artifactId>
            <version>${project.version}</version>
        </dependency>
        
    </dependencies>
```

## The mapping file

The concrete implementations needs to register their `Greeting` class as a implementation by adding a file in `/src/main/resources/META-INF/services/` called 
`com.github.phillipkruger.factory.api.Greeting` (The fully qualified name of the interface)

And the contents of the file is the name of the implementation, example Bugs Bunny:

``` bash
    com.github.phillipkruger.factory.impl.BugsBunny
```

## The factory

The factory now needs to get all instances of Greetings:

```java
    ServiceLoader<Greeting> loader = ServiceLoader.load(Greeting.class);
    Iterator<Greeting> greetingIterator = loader.iterator();
    while (greetingIterator.hasNext()) {
        Greeting greeting = greetingIterator.next();
        loadedGreetings.put(greeting.getName(), greeting);
    }
```

Now we got rid of the if-statement in the factory.

## Run the example:

In the spi folder:
```bash
    mvn clean install
```

This will build the project and also run the app. The log will output:

```bash
    SEVERE: 
    Good day Phillip.
    Goeie dag Phillip.
    Eeee, what's up Phillip ?
```

You can also run this outside of maven:

```bash
    java -jar spi-engine/target/spi-engine-1.0.0-SNAPSHOT-jar-with-dependencies.jar Johny Afrikaans
    SEVERE: 
    Goeie dag Johny.
```
## Also see

* [https://docs.oracle.com/javase/tutorial/sound/SPI-intro.html]

<hl/>

# Contexts and Dependency Injection (CDI)

The latest version of [CDI](http://docs.jboss.org/cdi/spec/2.0/cdi-spec.html) allows you to use CDI in Java SE. To create a factory, we are going to create our 
[own annotation](https://github.com/phillip-kruger/factories-example/blob/master/cdi/cdi-api/src/main/java/com/github/phillipkruger/factory/api/GreetingProvider.java)
as part of the API called `GreetingProvider`:

```java
    @Qualifier
    @Retention(RetentionPolicy.RUNTIME)
    @Target(ElementType.TYPE)
    public @interface GreetingProvider {
        String value();
    }
```

And a [literal implementation](https://github.com/phillip-kruger/factories-example/blob/master/cdi/cdi-api/src/main/java/com/github/phillipkruger/factory/api/GreetingProviderLiteral.java) for the above:

```java
    @AllArgsConstructor
    public class GreetingProviderLiteral extends AnnotationLiteral<GreetingProvider> implements GreetingProvider {

        private final String name;

        @Override
        public String value() {
            return this.name;
        }
    }
```

This will allow up to annotate any concrete implementation (that is now a Request scoped CDI Bean) with `@GreetingProvider`, example English:

```java
    @GreetingProvider("English")
    @RequestScoped
    public class English implements Greeting {

        @Override
        public String sayHello(String to) {
            return "Good day " + to + ".";
        }

        @Override
        public String getName() {
            return "English";
        }
    }
```

The factory change to find all `@GreetingProvider` classes:

```java
    public class GreetingFactory {

        @Inject @Any
        private Instance<Greeting> greetings;

        public Greeting getGreeting(String name) {
            Instance<Greeting> instance = greetings.select(new GreetingProviderLiteral(name));
            if(!instance.isUnsatisfied()){
                Greeting provider = instance.get();
                return provider;
            }else{
                return new English();
            }
        }

    }
```

So now we do not need the SPI mapping file anymore, there is not if-statement in the factory, but we still have to update the dependencies to include all implementations we want.

## Run the example:

In the cdi folder:
```bash
    mvn clean install
```

This will build the project and also run the app. The log will output:

```bash
    SEVERE: 
    Good day Phillip.
    Goeie dag Phillip.
    Eeee, what's up Phillip ?
```

You can also run this outside of maven:

```bash
    java -jar cdi-engine/target/cdi-engine-1.0.0-SNAPSHOT-jar-with-dependencies.jar Charmaine BugsBunny
    SEVERE: 
    Eeee, what's up Charmaine ?
```
## Also see

* [http://www.mastertheboss.com/jboss-frameworks/cdi/building-a-cdi-2-standalone-java-application]
* [http://www.adam-bien.com/roller/abien/entry/injecting_classes_in_java_se]

<hl/>

# CDI on Java EE

We can of course also use CDI to create the solution on an Application Server. We will now make the entry point a REST service (rather than a main method) so we need to create 
add a `ApplicationConfig` to enable JAX-RS:

```java
    @ApplicationPath("/api")
    public class ApplicationConfig extends Application {
    }
``` 

The `GreetingService` now become a REST Resource that allow you to do a `GET` passing the name as a `PathParam` and optional ways as `QueryParam`:

```java
    @Path("/")
    @Produces(MediaType.APPLICATION_JSON)
    public class GreetingService {

        @Inject
        private GreetingFactory factory;

        @GET
        @Path("{to}")
        public String sayHello(@PathParam("to") String to, @QueryParam("way") List<String> way){
            //....
        }
    }
```

The factory and annotated `RequestScoped` CDI Bean implementations stays exactly the same as the CDI on Java SE example.

## Run the example:

This example can run on 3 different application servers (just to show the portability of Java EE)

* [Wildfly Swarm](http://wildfly-swarm.io/)
* [Open Liberty](https://openliberty.io/)
* [Payara Micro](https://www.payara.fish/payara_micro)

(You **do not** have to download, install or configure anything, the maven script will do that)

In the javaee-cdi folder:
```bash
    mvn clean install -P wildfly
```

or

```bash
    mvn clean install -P liberty
```

or

```bash
    mvn clean install -P payara
```

In all 3 cases maven will 
* start the application server with the app deployed
* hit 2 REST Url's:
** http://localhost:8080/javaee-cdi-engine/api (This list all implementations)
** http://localhost:8080/javaee-cdi-engine/api/Phillip (This says hello to Phillip with all implementations)
* shutdown the application server (except payara)

So in the log you will see (something like):


```bash
===============================================
["BugsBunny","Afrikaans","English"]
===============================================

===============================================
["Eeee, what's up Phillip ?","Goeie dag Phillip.","Good day Phillip."]
===============================================
```

If you run payara, the server does not shutdown, so you can also manually test the factory:

```bash
    wget -qO- http://localhost:8080/javaee-cdi-engine/api/Donald?way=BugsBunny
    ["Eeee, what's up Donald ?"]
```

<hl/>

# EJB on Java EE

Just to complete the examples, here is how you can do this with EJB's on Java EE (so no CDI - and thus also not custom annotation)

The `GreetingService` stays the same as the Java EE CDI example, so we still have a REST entry point. The concrete implementations now change to become EJB, example English:

```java
    @Stateless
    @EJB(beanInterface = Greeting.class, beanName = "English", name = "English")
    public class English implements Greeting {

        @Override
        public String sayHello(String to) {
            return "Good day " + to + ".";
        }

        @Override
        public String getName() {
            return "English";
        }
    }

```

The factory now does a JNDI lookup based on the bean name:

```java
    @Log
    @Stateless
    public class GreetingFactory {

        @EJB(lookup = "java:module/English")
        private Greeting english; // default

        public Greeting getGreeting(String name) {
            Greeting g = lookup("java:module/" + name);
            if(g==null)return english;
            return g;
        }

        public List<Greeting> getAll(){

            List<Greeting> greetings = new ArrayList<>();

            try {

                InitialContext context = new InitialContext();

                NamingEnumeration<Binding> list = (NamingEnumeration<Binding>)context.listBindings("java:global/javaee-ejb-engine");    

                while (list.hasMore()) {
                    Binding next = list.next();
                    if(next.getName().endsWith(Greeting.class.getName())){
                        //Greeting g = (Greeting)next.getObject(); // Not the case with Glassfish :( (com.sun.ejb.containers.JavaGlobalJndiNamingObjectProxy)
                        Greeting g = lookup("java:global/javaee-ejb-engine/" + next.getName());
                        if(g!=null && !greetings.contains(g))greetings.add(g);
                    }
                }


            } catch (NamingException e) {
                throw new RuntimeException(e);
            } 
            return greetings;
        }

        private Greeting lookup(String jndi){
            try {
                InitialContext context = new InitialContext();
                Object o = context.lookup(jndi);
                return (Greeting)o;
            } catch (NamingException e) {
                log.log(Level.SEVERE, "Could not lookup [{0}]", jndi);
                return null;
            }   
        }
    }

```

## Run the example:

Simular to the Java EE CDI example, this runs on [Wildfly Swarm](http://wildfly-swarm.io/), [Open Liberty](https://openliberty.io/) and [Payara Micro](https://www.payara.fish/payara_micro)

Example:

In the javaee-ejb folder:

```bash
    mvn clean install -P wildfly
```
(or -P liberty or -P payara)

In all 3 cases maven will 
* start the application server with the app deployed
* hit 2 REST Url's:
** http://localhost:8080/javaee-ejb-engine/api (This list all implementations)
** http://localhost:8080/javaee-ejb-engine/api/Phillip (This says hello to Phillip with all implementations)
* shutdown the application server (except payara)

So in the log you will see (something like):


```bash
===============================================
["BugsBunny","Afrikaans","English"]
===============================================

===============================================
["Eeee, what's up Phillip ?","Goeie dag Phillip.","Good day Phillip."]
===============================================
```

If you run payara, the server does not shutdown, so you can also manually test the factory:

```bash
    wget -qO- http://localhost:8080/javaee-ejb-engine/api/Barney?way=Afrikaans
    ["Goeie dag Barney."]
```

<hl/>

# Dynamic SPI

So up until now, the only thing we have to do when adding a new implementation is to create the module that contains the `Greeting` implementation and update the `pom.xml` to include
the new dependency.

Next let's see how to load the new implementation dynamically (so no need to update the dependency).

The implementations are exactly like the SPI example, including the mapping file, but now we can remove the modules as dependencies in the pom.xml:

```xml
    <dependencies>
        <dependency>
            <groupId>${project.groupId}</groupId>
            <artifactId>spi-api</artifactId>
            <version>${project.version}</version>
        </dependency>
        
        <!-- This will be loaded dynamically
        <dependency>
            <groupId>${project.groupId}</groupId>
            <artifactId>spi-impl-afrikaans</artifactId>
            <version>${project.version}</version>
        </dependency>
        
        <dependency>
            <groupId>${project.groupId}</groupId>
            <artifactId>spi-impl-bugsbunny</artifactId>
            <version>${project.version}</version>
        </dependency>
        -->
    </dependencies>
```

And the factory looks like this:

```java
    public class GreetingFactory {

        private final Map<String,Greeting> loadedGreetings = new HashMap<>();

        public GreetingFactory(){

            URLClassLoader classloader = getURLClassLoader();

            ServiceLoader<Greeting> loader = ServiceLoader.load(Greeting.class, classloader);
            Iterator<Greeting> greetingIterator = loader.iterator();
            while (greetingIterator.hasNext()) {
                Greeting greeting = greetingIterator.next();
                loadedGreetings.put(greeting.getName(), greeting);
            }
        }

        public Greeting getGreeting(String name){
            if(loadedGreetings.containsKey(name)){
                return loadedGreetings.get(name);
            }else {
                return new English();
            }
        }

        private URLClassLoader getURLClassLoader(){

            File[] pluginFiles = getPluginFiles();

            ArrayList<URL> urls = new ArrayList<>();
            for(File plugin:pluginFiles){
                try{
                    URL pluginURL = plugin.toURI().toURL();
                    urls.add(pluginURL);
                }catch(MalformedURLException m){
                    log.log(Level.SEVERE, "Could not load [{0}], ignoring", plugin.getName());
                }

            }

            return new URLClassLoader(urls.toArray(new URL[]{}),GreetingFactory.class.getClassLoader());
        }

        private File[] getPluginFiles(){
            File loc = new File("plugins");
            File[] pluginFiles = loc.listFiles((File file) -> file.getPath().toLowerCase().endsWith(".jar"));
            return pluginFiles;
        }
    }
```

Basically, the factory will still use SPI `ServiceLoader` to load the available greetings, but we pass a custom classloader in. 
We look for any jar file in plugins folder and load those with `URLClassloader`.

This means I can now just create a new implementation module and drop the file in the `plugins` folder. Nice and plug-able.

## Run the example:

In the dynamic-spi folder:
```bash
    mvn clean install
```

This will build the project and also run the app. The log will output:

```bash
    SEVERE: 
    Good day Phillip.
    Goeie dag Phillip.
    Eeee, what's up Phillip ?
```

You can also run this outside of maven:

```bash
    java -jar dynamic-spi-engine/target/dynamic-spi-engine-1.0.0-SNAPSHOT-jar-with-dependencies.jar Madonna BugsBunny
    SEVERE: 
    Eeee, what's up Madonna ?
```

<hl/>

# Dynamic SPI on Java EE

Now whether this is a good idea is a different discussion, but just to show that it's possible, we will now use dynamic SPI to load the implementations on an Application server.
This means I can add new implementation to a running server. So not only can I add a new implementation without touching the code or the dependencies, but I can also enable this new 
implementation without having to restart the application. 

The implementations looks exactly like the SPI example, the `pom.xml` does not contain any implementation modules, and we now have a new class to load the module in the plugins folder:

It's an Application Scoped CDI Bean (so singleton) that loads modules on startup. The modules can also be reloaded with REST:

```java
    @Path("/pluginloader")
    @ApplicationScoped
    @Log
    public class PluginLoader {

        @Produces @Named("Greetings")
        private final Map<String,Greeting> loadedGreetings = new HashMap<>();

        public void init(@Observes @Initialized(ApplicationScoped.class) ServletContext context) {
            loadPlugins();
        }


        @GET
        @Path("/reload")
        public Response loadPlugins(){
            ClassLoader classloader = getClassLoader();

            ServiceLoader<Greeting> loader = ServiceLoader.load(Greeting.class, classloader);
            Iterator<Greeting> greetingIterator = loader.iterator();
            while (greetingIterator.hasNext()) {
                Greeting greeting = greetingIterator.next();
                log.log(Level.SEVERE, "Adding provider [{0}]", greeting.getName());
                if(!loadedGreetings.containsKey(greeting.getName())){
                    loadedGreetings.put(greeting.getName(), greeting);
                }
            }

            return Response.ok("ok").build();
        }

        private ClassLoader getClassLoader(){

            File[] pluginFiles = getPluginFiles();
            if(pluginFiles!=null){        
                ArrayList<URL> urls = new ArrayList<>();
                for(File plugin:pluginFiles){
                    try{
                        URL pluginURL = plugin.toURI().toURL();
                        urls.add(pluginURL);
                    }catch(MalformedURLException m){
                        log.log(Level.SEVERE, "Could not load [{0}], ignoring", plugin.getName());
                    }
                }
                return new URLClassLoader(urls.toArray(new URL[]{}),this.getClass().getClassLoader());
            }
            return this.getClass().getClassLoader();
        }

        private File[] getPluginFiles(){

            File loc = getPluginDirectory();
            if(loc==null)return null;
            File[] pluginFiles = loc.listFiles((File file) -> file.getPath().toLowerCase().endsWith(".jar"));
            return pluginFiles;

        }

        private File getPluginDirectory(){
            File plugins = new File("plugins");
            if(plugins.exists())return plugins1;  

            return null;
        }
    }
```

All the loaded `Greetings` is available in a Map<String,Greeting> that can be injected into the factory:

```java
    @RequestScoped
    @Log
    public class GreetingFactory {

        @Inject @Named("Greetings")
        private Map<String,Greeting> greetings;

        // ...
    }
```

## Run the example:

Simular to the Java EE CDI and EJB example, this runs on [Wildfly Swarm](http://wildfly-swarm.io/), [Open Liberty](https://openliberty.io/) and [Payara Micro](https://www.payara.fish/payara_micro)

Example:

In the javaee-spi folder:

```bash
    mvn clean install -P wildfly
```
(or -P liberty or -P payara)

In all 3 cases maven will 
* start the application server with the app deployed
* hit 2 REST Url's:
** http://localhost:8080/javaee-spi-engine/api (This list all implementations)
** http://localhost:8080/javaee-spi-engine/api/Phillip (This says hello to Phillip with all implementations)
* shutdown the application server (except payara)

So in the log you will see (something like):


```bash
===============================================
["BugsBunny","Afrikaans","English"]
===============================================

===============================================
["Eeee, what's up Phillip ?","Goeie dag Phillip.","Good day Phillip."]
===============================================
```

If you run payara, the server does not shutdown, so you can also manually test the factory:

```bash
    wget -qO- http://localhost:8080/javaee-spi-engine/api/Frans?way=Afrikaans
    ["Goeie dag Frans."]
```

Currently in the plugins folder we have:

```bash
    ls javaee-spi-engine/plugins/
    javaee-spi-impl-afrikaans-1.0.0-SNAPSHOT.jar  javaee-spi-impl-bugsbunny-1.0.0-SNAPSHOT.jar
```

That was copied there when we build those implementations.

Now, let's leave the server running, and add a new way to greet you called **Ali G.**
(see [https://www.youtube.com/watch?v=b00lc92lExw])

```bash
    cd javaee-spi-impl-alig
    mvn clean install -P plugin
```

This will copy the Ali G example to the plugin folder.

Now let's greet Frans again:

```bash
    wget -qO- http://localhost:8080/javaee-spi-engine/api/Frans?way=AliG
    ["Booyakasha Frans !"]
```

