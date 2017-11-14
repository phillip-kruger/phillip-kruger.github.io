---
title: Fatjars, Thinwars and why OpenLiberty is cool.
tags: [Java EE, OpenLiberty]
image: "/images/openliberty.jpg"
bigimg: "/images/Fatjars_Thinwars_and_why_OpenLiberty_is_cool/banner.jpg"
---

# Fatjars.

Building a Fatjar (or Uberjar) that contains everything you need to run you application nicely packaged together means you can just do:

```bash
java -jar myapp.jar 
```

and of you go. No Application server. No classpath. 

This approach has been popularised by the microservices architectural style and frameworks like [Springboot](https://projects.spring.io/spring-boot/)

*"In short, the [microservice architectural style](https://martinfowler.com/articles/microservices.html) is an approach to developing a **single application** 
as a suite of **small services**, each running in its **own process** and communicating with lightweight mechanisms, often an HTTP resource API. 
These services are built around business capabilities and **independently deployable** by fully automated deployment machinery"*

Having a bunch of executable jar files tick all the boxes above. 

# Java EE.
The fatjar concept has also been available in Java EE for a while now. All the lightweight application servers has a "Micro" option:

* [WildFly Swarm](http://wildfly-swarm.io/)
* [Payara Micro](https://www.payara.fish/payara_micro)
* [TomEE](http://openejb.apache.org/)
* [KumuluzEE](https://ee.kumuluz.com/)
* [Meecrowave](http://openwebbeans.apache.org/meecrowave/index.html)

As mentioned, there are many pros to having a fatjar deployment. However, there are some cons as well.

* You make this choice at development time (and it's actually a deployment time choice). I like to separate my development model from my deployment model.
* This gives you a sub-optimal development cycle. You need to build a fatjar, then stop the previous version, most likely with a kill, then do a start again. 
The turnaround from "code change" to "code running" gets annoyingly long after a while. One of the benefits of deploying a thin war to a running application server is the quick turnaround.
(Adam Bien explain it better in his video "[Thin WARs, Java EE 7, Docker and Productivity](https://www.youtube.com/watch?v=5N4EUDhrkec)")
* Not having a classpath is actually a pro and a con. Even though one of the advertised advantages of fatjars is not having an application server, you actually still have an application server.
It's just embedded. Having only one jar file means your application and the embedded application server has the same dependencies. You might run into issues where your application uses
another version of a dependency than the embedded server. This can cause some nice hidden bugs. Having the ability to isolate the application server classpath from you application is actually a good thing.
Java 9 might solve this, however most application servers are still running on Java 8. 

# Docker.
Docker has taken the microservices approach a level deeper and allow you to isolate on OS level. This means building separated jar files becomes less relevant as you will be building 
separated Docker images. 

Building a fat jar to be deployed as a Docker image is actually slower and heavier than a thin war. You typically layer you Docker images:

![](/images/Fatjars_Thinwars_and_why_OpenLiberty_is_cool/docker_layering.png)

(above: your final layer in the fatjar option is much heavier than the thinwar option)

# OpenLiberty is cool !

Traditional Websphere is big, slow, expensive and difficult to install. Not something you would use to build Microservices with.
IBM is a fairly late entry to the lightweight application server solutions with [Websphere Liberty](https://developer.ibm.com/wasdev/websphere-liberty/), 
of which the core has been open-sourced recently under [OpenLiberty](https://openliberty.io/).

But this late entry might be the reason why have done certain things right and very clean. The way that you can only load the parts that you need with features, 
and how you can extend the server with your own features, is awesome. And even though underlying other application server are also doing some modularity with OSGi (or JBoss Modules), 
it's just easier with Liberty. Including [Microprofile](https://microprofile.io/) is just another feature. Other application servers has added MicroProfile to their fatjar ("Micro") distributions,
and even though I believe it's possible to also add it to the full application server release, it's not easy to do.

The other cool thing is how you can very easily decide the deployment model only at deploy time. So you can have the best of all worlds.
You can develop against a full application server with the thinwar model (quick turnaround).
When building you can assemble a fatjar, thinwar, docker image or all of them. What you develop against stays the same.

## OpenLiberty with MicroProfile example.

I have create a simple application to demonstrate this deployment options. (Code is available in [GitHub](https://github.com/phillip-kruger/quote-service))

I did not want to build a basic "Hello world", as I want to include some of the MicroProfile features, so this is a "Quote of the Day" app. 
It uses a factory to load a quote provider (there is only one for now). The current provider gets and caches a quote from [forismatic.com](http://forismatic.com/en/api/). 
I use the [MicroProfile Configuration API](https://www.ibm.com/support/knowledgecenter/en/was_beta_liberty/com.ibm.websphere.wlp.nd.multiplatform.doc/ae/cwlp_microprofile_overview.html) 
to configure things like the HTTP Proxy, the URL and the provider to load. I use the [MicroProfile Fault Tolerance API](https://www.ibm.com/support/knowledgecenter/en/was_beta_liberty/com.ibm.websphere.liberty.autogen.beta.doc/ae/rwlp_feature_mpFaultTolerance-1.0.html) 
to make sure we survive when the provider source is not available.

### Configuring OpenLiberty
Configuration on OpenLiberty is also very clean. This makes it easy to include the configuration in your project. 
Using maven resource filtering you can also extract certain variables to your build. 
Here the important parts of my `server.xml` (you can see the full one in [github](https://github.com/phillip-kruger/quote-service/blob/master/src/main/liberty/config/server.xml))

**src/main/liberty/config/server.xml**

```xml
<?xml version="1.0" encoding="UTF-8"?>
<server description="${project.build.finalName}">

    <featureManager>
        <feature>javaee-7.0</feature>
        <feature>microProfile-1.2</feature>
    </featureManager>

    <httpEndpoint id="defaultHttpEndpoint"
        httpPort="${httpPort}"
        httpsPort="${httpsPort}"/>

    <application location="${project.build.directory}/${project.build.finalName}.war"/>

    <logging traceSpecification="${log.name}.*=${log.level}"/>

</server>
```

For now we just include the umbrella features for Java EE and Microprofile. A bit later we can fine tune that to reduce the memory footprint.

The `${httpPort}` and `${httpsPort}` will actually come from 
[bootstrap.properties](https://www.ibm.com/support/knowledgecenter/en/SSAW57_liberty/com.ibm.websphere.wlp.nd.multiplatform.doc/ae/twlp_inst_bootstrap.html) 
that we create with the [liberty maven plugin](https://github.com/WASdev/ci.maven).

All variables in the `server.xml`, including `${project.build.directory}` and `${project.build.finalName}` will be replace when we build with this resource filtering in the pom.xml:

```xml
<build>
    <finalName>${project.artifactId}</finalName>
    <resources>
        <resource>
            <directory>${basedir}/src/main/liberty/config</directory>
            <targetPath>${project.build.directory}</targetPath>
            <filtering>true</filtering>
            <includes>
                <include>server.xml</include>
            </includes>
        </resource>
    </resources>
</build>
```
(You can see the full pom.xml in [github](https://github.com/phillip-kruger/quote-service/blob/master/pom.xml))

So when we do a `mvn clean install` the `server.xml` will be copied to the target directory with the variables replaced.

### Deployment options
I am using [maven profiles](http://maven.apache.org/guides/introduction/introduction-to-profiles.html) to allow you to select, at build time, what deployment you want:

In the `<build>` of your [pom.xml]((https://github.com/phillip-kruger/quote-service/blob/master/pom.xml))

```xml
<plugins>
    <plugin>
        <groupId>net.wasdev.wlp.maven.plugins</groupId>
        <artifactId>liberty-maven-plugin</artifactId>
        <version>${openliberty.maven.version}</version>

        <configuration>
            <assemblyArtifact>
                <groupId>io.openliberty</groupId>
                <artifactId>openliberty-runtime</artifactId>
                <version>${openliberty.version}</version>
                <type>zip</type>
            </assemblyArtifact>
        </configuration>
    </plugin>
</plugins>
```

#### On the fly option

This option follows the same development cycle than a fatjar cycle (although it does note create a jar file).
If you do a `mvn clean install -Pfatjar` it will install, configure (from your `server.xml`) and start a server in the foreground, in other words the mvn process does not finish
as the server start as part of the mvn process. To stop the server you need to `ctrl-c` the process. 

```xml
    <profile>
        <id>fatjar</id>
        <activation>
            <property>
                <name>fatjar</name>
            </property>
        </activation>
        <build>
            <plugins>
                <plugin>
                    <groupId>net.wasdev.wlp.maven.plugins</groupId>
                    <artifactId>liberty-maven-plugin</artifactId>

                    <executions>
                        <execution>
                            <phase>install</phase>
                            <goals>
                                <goal>install-server</goal>
                                <goal>create-server</goal>
                                <goal>run-server</goal>    
                            </goals>

                            <configuration>
                                <configFile>${project.build.directory}/server.xml</configFile>
                                <bootstrapProperties>
                                    <httpPort>${openliberty.http.port}</httpPort>
                                    <httpsPort>${openliberty.https.port}</httpsPort>
                                </bootstrapProperties>
                                <jvmOptions>
                                    <param>-Xmx${openliberty.Xmx}</param>
                                </jvmOptions>
                            </configuration>
                        </execution>
                    </executions>
                </plugin>

            </plugins>
        </build>
    </profile>
```

Of course using an IDE like Netbeans (or any other IDE) this is actually just a button you click:

![](/images/Fatjars_Thinwars_and_why_OpenLiberty_is_cool/Netbeans.png)

#### Full application server option:

With this option we want to install, configure and start the server, and then deploy a thin war continuously as we write code. 
We still install and configure the server from scratch every time we start the server, just not on every deploy.

`mvn clean install -Pstart-liberty` will install, configure (from your `server.xml`) and start a liberty server in `/tmp` folder:

```xml
    <profile>
        <id>start-liberty</id>
        <activation>
            <property>
                <name>start-liberty</name>
            </property>
        </activation>
        <build>

            <plugins>
                <plugin>
                    <groupId>net.wasdev.wlp.maven.plugins</groupId>
                    <artifactId>liberty-maven-plugin</artifactId>

                    <executions>

                        <execution>
                            <id>1</id>
                            <phase>pre-integration-test</phase>
                            <goals>
                                <goal>install-server</goal>
                            </goals>
                            <configuration>
                                <assemblyInstallDirectory>${openliberty.installDir}</assemblyInstallDirectory>
                            </configuration>
                        </execution>

                        <execution>
                            <id>2</id>
                            <phase>pre-integration-test</phase>
                            <goals>
                                <goal>create-server</goal>
                                <goal>start-server</goal>
                            </goals>
                            <configuration>
                                <installDirectory>${openliberty.installDir}/wlp</installDirectory>
                                <serverName>${project.artifactId}</serverName>
                                <configFile>${project.build.directory}/server.xml</configFile>
                                <bootstrapProperties>
                                    <httpPort>${openliberty.http.port}</httpPort>
                                    <httpsPort>${openliberty.https.port}</httpsPort>
                                </bootstrapProperties> 
                                <jvmOptions>
                                    <param>-Xmx${openliberty.Xmx}</param>
                                </jvmOptions>
                            </configuration>
                        </execution>

                    </executions>
                </plugin>

            </plugins>
        </build>
    </profile>
```

You can now deploy the thinwar continuously:

`mvn clean install -Pdeploy`

```xml
<profile>
    <id>deploy</id>
    <activation>
        <property>
            <name>deploy</name>
        </property>
    </activation>
    <build>
        <plugins>
            <plugin>
                <groupId>net.wasdev.wlp.maven.plugins</groupId>
                <artifactId>liberty-maven-plugin</artifactId>
                <executions>
                    <execution>
                        <phase>pre-integration-test</phase>
                        <goals>
                            <goal>deploy</goal>
                        </goals>
                        <configuration>
                            <appArchive>${project.build.directory}/${project.artifactId}.war</appArchive>
                            <serverName>${project.artifactId}</serverName>
                            <installDirectory>${openliberty.installDir}/wlp</installDirectory>
                        </configuration>
                    </execution>
                </executions>
            </plugin>
        </plugins>
    </build>
</profile>
```

Stopping the server is also very easy:

`mvn clean install -Pstop-liberty`

#### Fatjar distribution

It's very easy to create a fatjar distribution with `mvn clean install -Ppackage-liberty`:

```xml
<profile>
    <id>package-liberty</id>
    <activation>
        <property>
            <name>package-liberty</name>
        </property>
    </activation>
    <build>
        <plugins>
            <plugin>
                <groupId>net.wasdev.wlp.maven.plugins</groupId>
                <artifactId>liberty-maven-plugin</artifactId>
                <executions>
                    <execution>
                        <phase>package</phase>
                        <goals>
                            <goal>package-server</goal>
                        </goals>
                        <configuration>
                            <packageFile>${project.build.directory}/${project.artifactId}.jar</packageFile>
                            <include>runnable</include>
                            <serverName>${project.artifactId}</serverName>
                            <installDirectory>${openliberty.installDir}/wlp</installDirectory>
                        </configuration>
                    </execution>
                </executions>
            </plugin>
        </plugins>
    </build>

</profile>
```

In my target directory I now have an executable (fat)jar file that I can start with:
`java -jar quote-service.jar`

In all the above mentioned `profiles` above you can test the example app with:

`mvn -Dtest=com.github.phillipkruger.quoteservice.QuoteApiIT surefire:test`

And that you give you a quote of the day:

```json
{
    "author":"Naguib Mahfouz",
    "text":"You can tell whether a man is clever by his answers. You can tell whether a man is wise by his questions."
}
```

### Fine-tuning the memory footprint.

To start off I just used the umbrella `javaee-7.0` and `microProfile-1.2` features, even though my application only use a subset of the specifications.

Using `jconsole` I measured the memory footprint (after a GC) of the running server: 

**50,691 kbytes**

You can change the `server.xml` to only include the features your app is using, in my example:

```xml
<feature>jaxrs-2.0</feature>
<feature>ejb-3.2</feature>
<feature>cdi-1.2</feature>
<feature>jsonp-1.0</feature>
<feature>jaxrsClient-2.0</feature>
<feature>mpConfig-1.1</feature>
<feature>mpFaultTolerance-1.0</feature>
```

Again using `jconsole` I measured the memory footprint (after a GC) of the running server:

**30,198 kbytes**

Ideally you would also fine-tune the `pom.xml` to only include the specification you use.

### More info

Also read this great blogs from [Pavel Pscheidl](https://twitter.com/PavelPscheidl)
* http://www.pavel.cool/javaee/java-ee-fatjars-docker/
* http://www.pavel.cool/javaee/ee4j/openliberty-jaxrs/

and watch this video from [Adam Bien](https://twitter.com/AdamBien)
* https://www.youtube.com/watch?v=5N4EUDhrkec