---
title: Hollowjars, Deployment scanner and why Wildfly swarm is cool.
tags: [Java EE, Wildfly swarm]
image: "/images/wildfly_swarm.png"
bigimg: "/images/Hollowjars_DeploymentScanner_and_why_WildflySwarm_is_cool/banner.jpg"
---

In a [previous post](/post/fatjars_thinwars_and_why_openliberty_is_cool/) I described how you can use [OpenLiberty](https://openliberty.io/) and [maven](https://maven.apache.org/index.html)
to start the server, either as a standalone, or as part of the maven build, and how to create a fatjar package.

In this post I am looking how to do this with Wildfly swarm. I could not get MicroProfile running Wildfly full, so this is not working the same as the OpenLiberty example.

I am using the same example project, with more maven profiles to run the different deployment options.

(see [https://github.com/phillip-kruger/javaee-servers-parent](https://github.com/phillip-kruger/javaee-servers-parent))
 
# Example project

I did not want to build a basic "Hello world", as I wanted to include some of the MicroProfile features, so this is a "Quote of the Day" app. 
It uses a factory to load a quote provider (there is only one for now). The current provider gets and caches a quote from [forismatic.com](http://forismatic.com/en/api/). 
I use the [MicroProfile Configuration API](https://www.ibm.com/support/knowledgecenter/en/was_beta_liberty/com.ibm.websphere.wlp.nd.multiplatform.doc/ae/cwlp_microprofile_overview.html) 
to configure things like the HTTP Proxy, the URL and the provider to load. I use the [MicroProfile Fault Tolerance API](https://www.ibm.com/support/knowledgecenter/en/was_beta_liberty/com.ibm.websphere.liberty.autogen.beta.doc/ae/rwlp_feature_mpFaultTolerance-1.0.html) 
to make sure we survive when the provider source is not available.

[https://github.com/phillip-kruger/quote-service](https://github.com/phillip-kruger/quote-service)

# Running as part of the maven build

You can use the `wildfly-swarm-plugin` to run (`mvn wildfly-swarm:run`) a wildfly swarm instance as part of the build. This plugin will do "fraction detection", meaning it will look at what parts of the application server you need
and only create a deployment with those fractions included. So you can still include the umbrella API's in your dependencies and code against those, but come deployment time, you will get 
a right size distribution. Cool !

```xml
    <dependencies>
        <!-- Java EE -->
        <dependency>
            <groupId>javax</groupId>
            <artifactId>javaee-api</artifactId>
            <version>${java-ee.version}</version>
            <scope>provided</scope>
        </dependency>
        <!-- MicroProfile -->
        <dependency>
            <groupId>org.eclipse.microprofile</groupId>
            <artifactId>microprofile</artifactId>
            <version>${microProfile.version}</version>
            <type>pom</type>
            <scope>provided</scope>
        </dependency>
    </dependencies>
```

I could not get filter resource to work using the run target. Seems like the plugin use the original source file before filter. I always use filtering when including HTML files that 
reference [webjars](https://www.webjars.org/), so this did not work for me. 

```xml
    <plugin>
        <groupId>org.apache.maven.plugins</groupId>
        <artifactId>maven-war-plugin</artifactId>
        <version>3.0.0</version>
        <configuration>
            <webResources>
                <resource>
                    <directory>${basedir}/src/main/webapp</directory>
                    <filtering>true</filtering>
                    <includes>
                        <include>**/*.css</include>
                        <include>**/*.jsp</include>
                    </includes>
                </resource>
            </webResources>
        </configuration>
    </plugin>
```

In this example I am using Semantic UI to build a webpage that display the quote of the day:

![](/images/Hollowjars_DeploymentScanner_and_why_WildflySwarm_is_cool/semanticui.png)

I use the maven properties for the versions of the CSS and JS in the HTML, and need to replace then with the real value when we build:

```html
    <link rel="stylesheet" type="text/css" href="webjars/semantic-ui/${semantic-ui.version}/dist/semantic.min.css">
    <script type="text/javascript" src="webjars/jquery/${jquery.version}/dist/jquery.min.js" />
    <script type="text/javascript" src="webjars/semantic-ui/${semantic-ui.version}/dist/semantic.min.js"></script>    
```

As an alternative I use the package goal and then the `exec-maven-plugin` to run the jar.

This also allows me to pass in a `standalone.xml` for any extra configuration:

```xml
    <plugin>
        <groupId>org.wildfly.swarm</groupId>
        <artifactId>wildfly-swarm-plugin</artifactId>
        <executions>
            <execution>
                <id>1</id>
                <phase>pre-integration-test</phase>
                <goals>
                    <goal>package</goal>
                </goals>
            </execution>
        </executions>
    </plugin>

    <plugin>
        <groupId>org.codehaus.mojo</groupId>
        <artifactId>exec-maven-plugin</artifactId>
        <version>1.6.0</version>
        <executions>
            <execution>
                <id>1</id>
                <phase>post-integration-test</phase>
                <goals>
                    <goal>exec</goal>
                </goals>
            </execution>
        </executions>
        <configuration>
            <executable>java</executable>
            <arguments>
                <argument>-jar</argument>
                <argument>${project.build.directory}${file.separator}${project.artifactId}-swarm.jar</argument>
                <argument>-c</argument>
                <argument>${project.build.directory}${file.separator}standalone.xml</argument>
            </arguments>
        </configuration>
    </plugin>
```

In my case the `standalone.xml` only contains the logging configuration, but you can now include any other configuration.

```xml
    <server xmlns="urn:jboss:domain:4.0">
        <profile>
            <subsystem xmlns="urn:jboss:domain:logging:3.0">
                <periodic-rotating-file-handler name="FILE" autoflush="true">
                    <file path="${wildfly-swarm.logfile}"/>
                    <suffix value=".yyyy-MM-dd"/>
                    <append value="true"/>
                </periodic-rotating-file-handler>
                <root-logger>
                    <level name="INFO"/>
                    <handlers>
                        <handler name="FILE"/>
                    </handlers>
                </root-logger>
                <logger category="${log.name}">
                    <level name="${log.level}"/>
                </logger>
            </subsystem>
        </profile>
    </server>
```

So in the `qoute-service` example you can just do this (same as the OpenLiberty example):

`mvn clean install -P wildfly-swarm-fatjar`


# Hollowjar

Wildfly swarm allows you to create a hollowjar. (see [this article](https://developers.redhat.com/blog/2017/08/24/the-skinny-on-fat-thin-hollow-and-uber/)) That is, a fatjar without your application, just the application server part. You can then provide the application as a command line input:

```bash
java -jar myapp-hollow-swarm.jar myapp.war
```

So if we can get a way to reload the app part, I can have the same development model as with a full application  (hot deploy)

# Deployment scanner

Wildfly swarm has a fraction called [deployment scanner](http://docs.wildfly-swarm.io/2017.12.1/#_deployment_scanner), that you can include in your distribution (fat or hollow).

The fraction detection will not auto detect this (as there is no reference to this in the code). Luckily you can define additional fraction in maven:

```xml
    <plugin>
        <groupId>org.wildfly.swarm</groupId>
        <artifactId>wildfly-swarm-plugin</artifactId>
        <executions>
            <execution>
                <phase>pre-integration-test</phase>
                <goals>
                    <goal>package</goal>
                </goals>
            </execution>
        </executions>
        <configuration>
            <hollow>true</hollow>
            <additionalFractions>scanner</additionalFractions>
        </configuration>
    </plugin>
```   

For this scanner fraction to work, add this to your `standalone.xml`

```xml
    <subsystem xmlns="urn:jboss:domain:deployment-scanner:2.0">
	<deployment-scanner 
		scan-enabled="true"
		scan-interval="5000" 
		path="/tmp/quote-service/wildfly-swarm/deployments" 
		name="quote-service" 
		auto-deploy-xml="false"/> 
    </subsystem>
``` 

If you now move an updated version of your app to the defined path you have hot deploy.

In the quote example, this means you can:

* mvn clean install -P wildfly-swarm-start (to start the server)
* mvn clean install -P wildfly-swarm-deploy (to hot deploy to the running server)
* mvn clean install -P wildfly-swarm-stop (to stop the running server)

You can also create a fatjar:

* mvn clean install -P package

# Saving time

To build and start a fatjar takes about **10 seconds**. To do a hot deploy takes about **2.7 seconds**.

That is a huge time saving, making the turn around time between changes much quicker.