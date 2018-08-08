---
title: Your own MicroProfile Config source
tags: [MicroProfile, Payara, Configuration]
image: "/images/MicroProfile.jpg"
bigimg: "/images/MicroProfile_config_source/banner.jpg"
---

[MicroProfile Config](https://github.com/eclipse/microprofile-config), that is part of the [MicroProfile](https://microprofile.io/) Specification, is the standardization for Java Enterprise and Microservices configuration. 

Out of the box (i.e. mandatory for all implementations as defined by the specification) there are 3 ways to define your configuration:

* ```System.getProperties()```
* ```System.getenv()```
* All ```META-INF/microprofile-config.properties``` on the classpath

The ```ordinal``` of these Config Sources determine the order in which the System will look for a certain property.

So if you have a Config property with the key of ```myservice.hostname```, you will inject it in your code:


```java
    @Inject @ConfigProperty(name = "myservice.hostname", defaultValue = "localhost")
    private String myServiceHostname;
```

The system will first see if there are a System property with the key ```myservice.hostname```, if not it will try Environment variables, then all ```microprofile-config.property``` files on the classpath.
If it could not find it anywhere, it will fallback to the ```defaultValue``` in the annotation.

# Your own config source.

You can also provide your own config source(s) and define the load order for that source. The Config Api uses [SPI](Service Provider Interfaces) to load all config sources, so it's pretty easy to create your own.

For example, say we want a source that loads first (i.e. event before System properties) and we store those config value in memory, we can write a class that extends ```org.eclipse.microprofile.config.spi.ConfigSource```:

```java
    
    public class MemoryConfigSource implements ConfigSource {
    
        public static final String NAME = "MemoryConfigSource";
        private static final Map<String,String> PROPERTIES = new HashMap<>();

        @Override
        public int getOrdinal() {
            return 900;
        }

        @Override
        public Map<String, String> getProperties() {
            return PROPERTIES;
        }

        @Override
        public String getValue(String key) {
            if(PROPERTIES.containsKey(key)){
                return PROPERTIES.get(key);
            }
            return null;
        }

        @Override
        public String getName() {
            return NAME;
        }
    }
```

(see the full source [here](https://github.com/phillip-kruger/microprofile-extentions/blob/master/config-ext/src/main/java/com/github/phillipkruger/microprofileextentions/config/MemoryConfigSource.java))

You also (as per SPI) register your implementation in ```META-INF/services``` by adding a entry in a file called ```org.eclipse.microprofile.config.spi.ConfigSource```

```
    com.github.phillipkruger.microprofileextentions.config.MemoryConfigSource
```

(full example [here](https://github.com/phillip-kruger/microprofile-extentions/blob/master/config-ext/src/main/resources/META-INF/services/org.eclipse.microprofile.config.spi.ConfigSource))

Above is a fairly simple example, just keeping config values in a static map. You can then create a JAX-RS service ([example](https://github.com/phillip-kruger/microprofile-extentions/blob/master/config-ext/src/main/java/com/github/phillipkruger/microprofileextentions/config/MemoryConfigApi.java)) to add and remove values from this map.

But what if you want a more complex config source? One that itself needs configuration ?

# Using MicroProfile Config to configure your own MicroProfile Config Source.

For example, if we want a Config source that find the values in [etcd](https://coreos.com/etcd/), we also need to configure the etcd server details. The good news is we can use the Config Api for that !

However, Config Source implementations is not CDI Beans, so you can not ```@Inject``` the values. You also need to ignore yourself (i.e when configuring your source do not look at your source, else you will be in an endless loop)

To get the Config without CDI is very easy:

```java
    Config config = ConfigProvider.getConfig();
```

(thanks to [Rudy De Busscher](https://twitter.com/rdebusscher) and others on the friendly [MicroProfile Google Group](https://groups.google.com/forum/#!topic/microprofile/rkkZJGJpxMU) for helping)

So now we just need to make sure to ignore ourself:

```java

    private String getPropertyValue(String key,String defaultValue){
        Config config = ConfigProvider.getConfig();
        Iterable<ConfigSource> configSources = config.getConfigSources();
        for(ConfigSource configsource:configSources){
            if(!configsource.getName().equals(NAME)){ // Ignoring myself
                String val = configsource.getValue(key);
                if(val!=null && !val.isEmpty())return val;
            }
        }
        return defaultValue;
        
    }
```

Where ```NAME``` is the name of your own config source.

(full example [here](https://github.com/phillip-kruger/microprofile-extentions/blob/master/config-ext/src/main/java/com/github/phillipkruger/microprofileextentions/config/EtcdConfigSource.java))

Now I can define the server details of my etcd server with any of the other config source options.

# Running the example.

I am running an [example](https://github.com/phillip-kruger/microprofile-extentions/tree/master/example) on [Payara-micro](https://www.payara.fish/) (but it should work on any MicroProfile implementation).

Using maven:

```xml

    <build>
        <finalName>${project.artifactId}</finalName>
        
        <plugins>
            
            <plugin>
                <groupId>fish.payara.maven.plugins</groupId>
                <artifactId>payara-micro-maven-plugin</artifactId>
                <version>1.0.1</version>
                <executions>
                    <execution>
                        <phase>pre-integration-test</phase>
                        <goals>
                            <goal>start</goal>
                        </goals>
                    </execution>
                </executions>
                <configuration>
                    <artifactItem>
                        <groupId>fish.payara.extras</groupId>
                        <artifactId>payara-micro</artifactId>
                        <version>${payara-micro.version}</version>
                    </artifactItem>
                    <deployWar>true</deployWar>
                    <!--<javaCommandLineOptions>
                        <option>
                            <value>-Dconfigsource.etcd.host=127.0.0.1</value>
                        </option>
                    </javaCommandLineOptions>-->
                </configuration>
    </plugin>
```

(see the full ```pom.xml``` [here](https://github.com/phillip-kruger/microprofile-extentions/blob/master/example/pom.xml))

If I uncomment ```javaCommandLineOptions``` I can change the etcd server host name, used in my etcd config source, to something else.

I can also use any of the other config sources to do this, for example, including a ```microprofile-config.properties``` in my example war file (like [this example](https://github.com/phillip-kruger/microprofile-extentions/blob/master/example/src/main/webapp/META-INF/microprofile-config.properties)), 
or use my other custom config source and change this in memory.

# Use it as a library.

You can also bundle all of this in a jar file to be used by any of your projects. I made the above available in [maven central](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22config-ext%22) and [github](https://github.com/phillip-kruger/microprofile-extentions/tree/master/config-ext), so you can also use that directly.

Just add this to your pom.xml

```xml
    <dependency>
        <groupId>com.github.phillip-kruger.microprofile-extentions</groupId>
        <artifactId>config-ext</artifactId>
        <version>1.0.7</version>
    </dependency>
```

And you have all of the above config sources.