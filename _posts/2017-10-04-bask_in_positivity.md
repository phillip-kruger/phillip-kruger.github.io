---
layout: post
title: Bask in positivity
tags: [JavaOne 2017]
bigimg: "/images/Bask_in_positivity/banner.jpg"
---

# Java EE: Heavyweight or Lightweight—Mythbusters
![](/images/Bask_in_positivity/adam1.jpg)

You can not attend a conference without going to an [Adam Bien](https://twitter.com/AdamBien) talk.
This talk (available [here](https://www.youtube.com/watch?v=LwimkQQDhio)) debunked the myth that Java EE is Heavyweight.

What is Heavyweight? Startup time? Memory size? CPU usage? Download size?

Here some metrics on RAM (to put it into perspective):

* Custom Socket server on JDK: *7MB*
* Wildfly: *35MB*
* Websphere Liberty: *40MB*
* Payara: *40MB*
* Tomcat / Jetty: *10MB*
* Chrome browser: *70MB*

CPU Overhead of EJB:

* 0.1 %

Uber jar (Fat jar):

* Payara Micro: *40MB* - No saving
* Wildfly Swarm: *30MB* - Saving 5MB, not worth it
* You do not save anything in terms of height
* When doing uberjar, you need to stop, rebuild, start the jar to test a change

Docker, Full application server and Thin wars.

* Using Docker and Full Application server, with your dependencies.
* Very light war file that can deploy quickly
* Light upload to cloud

![](/images/Bask_in_positivity/adam2.jpg)

# Modules and Services

Alex Buckley is the specification lead for the Java language and the Java Virtual Machine at Oracle. This talk explained how Java 9 Modules are changing the way we do [SPI](https://docs.oracle.com/javase/tutorial/ext/basics/spi.html) (Service Provider Interface) in Java, that is now a first class citizen in the language.

Oracle spent 10 year to clean up the JDK code and make that modular:

![](/images/Bask_in_positivity/module1.jpg)

You will now define the SPI in the module.java:

```java
module java.desktop {
  requires java.xml;
  export java.awt;
  user javax.print.PrintServiceLookup;
}
```

# Contemporary Java Web Applications with JSF 2.3
[Ed Burns](https://twitter.com/edburns) Co-Spec lead for JSF.

This talk (available [here](https://www.youtube.com/watch?v=yshXLB_HdhU)) went though the new features in JSF that is now included in Java EE 8.

![](/images/Bask_in_positivity/jsf.jpg)
Above one of the first JSF slides presented at JavaOne 16 years ago...

* Use of Servlet 4.0 server push - mostly under the cover, you get it for free. Performance enhancements
* WebSocket push - for intentional use case specific communication to the browser `@PushContext`
* Ajax method invocation - making a call to the sever from the client
* Multifield validation - now using CDI and Bean validation
* Tighter CDI integration - even in the View technology like Facelets `#injectedBean`
* Component search expressions

see https://javaserverfaces.github.io/

# Panel: Accelerating the Adoption of Java EE 8 with MicroProfile
![](/images/Bask_in_positivity/panel.jpg)

Panel discussion with the [Microprofile](https://twitter.com/MicroProfileIO) leads on the future of Microprofile, what the Java EE move to eclipse means for Microprofile and how they can assist with making the transition smooth.

*Microprofile might become the innovation engine for EE4J*

We (Multiply) should just add Microprofile to our Wildfly. (@Martin Coetzee)

It already has:

* JWT support with really easy annotations (also look at http://www.keycloak.org/)
* Fault tolerance (including Circuit Breaker)
* Configuration
* Metrics
* Healthcheck

# An Introduction to OAuth 2.0 and OpenID Connect

OAuth is the standardization of multiple companies that tried to solve the same problem, example, Google had AuthSub and Yahoo BBAuth.

The API economy changed that and forced the industry to standardize.
![](/images/Bask_in_positivity/oauth1.jpg)

OAuth does not have a User, it only deals with Authorization delegation. This is where OpenID Connect comes in.

The Speakers (Sanjay Rallapalliand Kiran Thakkar from Oracle) then showcased [IDCS](https://www.oracle.com/cloud/paas/identity-cloud-service.html) Oracle's Identity Cloud Service.

# Asynchronous API with CompletableFuture: Performance Tips and Tricks
This very advanced talk went into details on how you can get more out of [CompletableFuture](https://docs.oracle.com/javase/8/docs/api/java/util/concurrent/CompletableFuture.html)

![](/images/Bask_in_positivity/cf.jpg)

It was a bit hard to relate as the speaker (Sergey Kuksenko from Oracle) assumed you are already using and understanding CompletableFuture in you projects. That is not the case with us...

# Deconstructing and Evolving REST Security

![](/images/Bask_in_positivity/restsec.jpg)

One of my favorite talks of the conference by one of my favorite speakers, [David Blevins](https://twitter.com/dblevins) (also available [here](https://www.youtube.com/watch?v=9CJ_BAeOmW0))

![](/images/Bask_in_positivity/pi.jpg)

OAuth 2.0 = Session ID

OAuth 2.0 + JWT = Signed cookies.

# Portlet 3.0 Deep Dive

![](/images/Bask_in_positivity/portal.jpg)

This talk by Vernon Singleton from Liferay went through the new features in Portlet 3.0.

Some history:

* Portal 1.0 - Multiple applications aggregated on a single page.
* Portal 2.0 - Interportlet communication, Resources, Ajax
* Portal 3.0 - CDI, Single Page Applications, Portlet Hub

Some new features:

* New annotations for better programming model. `@Named @Inject @PortletRequestScoped @ActionMethod` and more.
* Annotation means you do not need portlet.xml anymore.
* Tighter integration into other Java EE Specs, like CDI
* PortalHub `portal.js` manage render state on the client.
* New view type called `HEADER_TYPE` allows partial response.

Also see:

* https://jcp.org/en/jsr/detail?id=362
* https://portals.apache.org/pluto/
* https://github.com/msnicklous/portals-pluto

# Oracle Cloudfest

We then went to Oracle Cloudfest at [AT&T Park](http://sanfrancisco.giants.mlb.com/sf/ballpark/) to watch Elle Golding and The Chainsmokers.

![](/images/Bask_in_positivity/fest1.jpg)
![](/images/Bask_in_positivity/fest2.jpg)
![](/images/Bask_in_positivity/fest3.jpg)
