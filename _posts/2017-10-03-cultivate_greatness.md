---
layout: post
title: Cultivate greatness
tags: [JavaOne 2017]
image: "/images/javaone_logo.jpg"
bigimg: "/images/Cultivate_greatness/banner.jpg"
---

# Demystifying Microservices for Java EE Developers

![](/images/Cultivate_greatness/mqtt.jpg)

This talk by [David Heffelfinger](https://twitter.com/ensode) and [Stephen Millidge](https://twitter.com/l33tj4v4) gave a quick overview of what Microservices are, discussed if this can be done in Java EE, explained strategies how to migrate from a Monolith to Microservices, and then showed showed some demo applications (with [MQTT](http://mqtt.org/)). They also discussed the possible upcoming features in Java EE 9 (Or EE4J)

* The modern Java EE implementations is lightweight and small and can be embedded
* The programming model is familiar and easy to use
* You have an uber jar (fat jar) deployment model option
* Can run in docker, as fat jar or thin war.

With a Microservices Architecture comes the [Fallacies of distributed computing](https://en.wikipedia.org/wiki/Fallacies_of_distributed_computing). So it's important to first check if you should be migrating to Microservices.

3 Options to migrate:

* Iterate refactoring
* Hybrid model
* Only new projects

Some features in [Microprofile](https://microprofile.io) that assist with Microservices type applications:

* Configuration API
* Fault tolerance (timeout, bulkhead, circuit breaker)
* JWT Propagation
* Metrix
* Healthcheck

Some possible feature in upcoming releases:

* Project Jigsaw support
* New Event API
* NoSQL database support
* New State Management API

The small demo using MQTT to communicate between two services was only using 27MB Memory and 64 MB file size on disk.

It could handle more that 700 requests per second.

# JSR 375: New Security APIs for Java EE

[Ivar Grimstad](https://twitter.com/ivar_grimstad) took us through some more detail on the new Security API (as mentioned in previous post)

![](/images/Cultivate_greatness/security.jpg)

The goals of this new API:

* Simplify security programming model
* Enable developers to manage security
* Layered APIs delegate to others
* Use CDI where appropriate

All of this can be used in Servlet, JAX-RS, JSP, MVC and more

### Authentication Mechanism

This is specifically done for Servlet.

Some new annotations:

```java
  @BasicAuthenticationMechanismDefinition
  @FormAuthenticationMechanismDefinition
  @CustomFormAuthenticationMechanismDefinition

  @LoginToContinue
  @RememberMe
  @AutoApplySession
```

### Identity Store

This is an abstract over the user and group store. You can also write your own, and stores can be chained (with added priority)

Some new annotations:

```java
  @LdapIdentityStoreDefinition
  @DatabaseIdentityStoreDefinition

  // Also using the existing
  @RolesAllowed
```

### Security Context (Caller)

* `getCallerPrincipal()`
* `getPrinsipalsByType(Class type)`
* `isCallerInRole(String role)`

### Some resources to check out:

* https://github.com/javaee/security-api
* https://github.com/javaee/security-spec
* https://github.com/javaee/security-soteria
* https://github.com/javaee/security-examples
* https://github.com/ivargrimstad/security-samples

# Connect Java EE to the Cloud with JCA

![](/images/Cultivate_greatness/jca.jpg)

[Stephen Millidge](https://twitter.com/l33tj4v4) explained the demo that is also available in the Payara Booth. It's basically Payara Micro Servers running on raspberry pies, talking to each other with MQTT and clustered CDI Events, connecting to Microsoft Azure IoT Hub with JCA (yes Sarel, they are using a 16 years old technology to connect to the cloud) Talking to a cluster of Payara Micros on Azure.

Very cool !! If you blow on the sensors you can see the graph on the Internet change due to the humidity.

*"The Java EE Connector architecture defines a standard architecture for connecting the Java EE Platform to heterogeneous Enterprise informations Systems"*

# JDK 9 Language, Tooling, and Library Features

![](/images/Cultivate_greatness/duke.jpg)

[Joe Darcy](https://twitter.com/jddarcy) went through some Non Jigsaw changes in JDK 9. Some leftovers from [Project Coin](http://openjdk.java.net/projects/coin/), [JShell and REPL](https://docs.oracle.com/javase/9/jshell/introduction-jshell.htm#JSHEL-GUID-630F27C8-1195-4989-9F6B-2C51D46F52C8), New JavaDoc and more.  

The whole presentation can be downloaded here:

http://www.jddarcy.org/Conferences/JavaOne/J1_2017-jdk9-lang-tools-libs.pdf

# Architecting for Failure: Why Are Distributed Systems Hard?

![](/images/Cultivate_greatness/distributing.jpg)

[Markus Eisele](https://twitter.com/myfear) from [Lightbend](https://www.lightbend.com/) talked about the history of software and how we moved from Mainframe -> Enterprise -> Cloud, and what this means for software development.

The he discussed the challenges the distributing computing brings, as we now can not rely on other systems to be available or responsive.

[Lagom](https://www.lightbend.com/lagom-framework), an open source microservices framework built on Akka and Play, can assist with building these types of system.

Some links:

* http://www.lightbend.com/lagom
* https://github.com/lagom
* https://github.com/lagom/online-auction-java

# Bring Your Favorite Kafka to Java EE with CDI

[Ivan St. Ivanov](https://twitter.com/ivan_stefanov) discussed the Extensibility of CDI by talk through an example CDI Extension that connects to Kafka. This talk was less about Kafka and more about CDI Extentions.

We went through the CDI lifecycle and the options available to hook into this using SPI.

![](/images/Cultivate_greatness/cdi_ext.jpg)

# Developer Keynote

![](/images/Cultivate_greatness/keynote.jpg)

The Keynote showcased some things from [OracleDevs](https://twitter.com/oracledevs), [Slack](https://twitter.com/SlackHQ) (and how it can be used in teams), some Oracle cloud stuff and more.

See the video here https://developer.oracle.com/videos

# Java EE: Revolutionizing Design Pattern Use

![](/images/Cultivate_greatness/patterns.jpg)

[Alex Theedom](https://twitter.com/alextheedom) gave a really nice talk on how simple and easy design patterns is with the Java EE programming Model and we discussed and showcased the following patterns

* Factory
* Singleton
* Observer
* Decorator

![](/images/Cultivate_greatness/decorator.jpg)

# MVC 1.0: Community Involvement Matters!

Oracle decided to not include the new Action based view technology (MVC 1.0) in the Java EE 8 Spec, and handed over to
[Ivar Grimstad](https://twitter.com/ivar_grimstad) who is now running with it.

The spec and implementation is basically feature complete.

This is very similar to Spring MVC and VRaptor, except that is's now a standard and it's built on JAX-RS and CDI.

![](/images/Cultivate_greatness/mvc.jpg)

How can we, the community, help ?

* Write
* Give feedback
* Blog
* Tweet
* Create a Logotype
* LaTex -> AsciiDoc (the spec documentation)

MVC comes with JSP and facelets support out of the box, but there is already community contributed support for:

* asciidoc
* freemarker
* handlebars
* jade
* jsr223
* mustache
* pebble
* stringtemplate
* thymeleaf
* velocity

Some links:

* https://www.mvc-spec.org
* https://github.com/mvc-spec
* https://github.com/ivargrimstad/mvc-samples


# IBM Liberty Open sourcing

![](/images/Cultivate_greatness/ibm.jpg)

I also had a chat with [Erin Schnabel](https://twitter.com/ebullientworks), the Liberty profile development lead, about the open sourcing of [Liberty](https://openliberty.io/).  

This is a full Java EE 7 and Microprofile server that is open source with no restrictions. This is very good news for us!

# Party One

Then I made some new friends at #PartyOne2017 !!

![](/images/Cultivate_greatness/p1.jpg)
The view

![](/images/Cultivate_greatness/p2.jpg)
With [Stephen Millidge](https://twitter.com/l33tj4v4), [Mike Croft](https://twitter.com/croft) from Payara and [David Blevins](https://twitter.com/dblevins) from Tomitribe.

![](/images/Cultivate_greatness/p3.jpg)
With [David Heffelfinger](https://twitter.com/ensode)
