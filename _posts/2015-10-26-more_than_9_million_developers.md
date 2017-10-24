---
layout: post
title: More than 9 million developers
tags: [JavaOne 2015]
bigimg: "/images/More_than_9_million_developers/session_1.jpg"
aliases:
    - /2015/10/more-than-9-million-developers.html
---
9 sessions and one party. The first day of the conference was absolutely great !

Here a quick note on the session I attended.

# Introduction to Java 8: JVM, Language, and Platform (Timothy Fagan & Dario Laverde)
A very interesting talk on some of the awesome new features in Java 8 with some demo code. Some of my favourites included:

* Changes to the logger api
* New time api
* Enhancements to Map
* default (existing keyword for the switch statement) can now also be used as a method construct in an interface.
* [Optional](https://docs.oracle.com/javase/8/docs/api/java/util/Optional.html) is a new class that could solve the NullPointerException or the if not null check.
* Java -> JavaScripts and JavaScript -> Java

# WebSocket Perspectives: Clouds, Streaming, Microservices, and the Web of Things ([Frank Greco](https://www.linkedin.com/in/frankdgreco))
![session_2](/images/More_than_9_million_developers/session_2.jpg)
This talk gave me a new perspective on the possibilities of WebSockets. Although WebSockets have been around for a while, this talk explained how this potentially could (or should?) be used in some use-cases where we currently use REST.

REST really is an synchronous RPC call. Pretty soon we will realize we really need asynchronous calls. WebSockets is a full duplex asynchronous API for the web (like TCP for the web)

For some awesome demos see [http://kaazing.org/demos/](http://kaazing.org/demos/)

# Meet James Gosling and NetBeans Community Members
![session_3](/images/More_than_9_million_developers/session_3.jpg)
This session was a [NetBeans](https://netbeans.org/) community session from a panel of NetBeans users. For those who do not know, NetBeans is the [IDE](https://en.wikipedia.org/wiki/Integrated_development_environment) we use to write code.

If you are a Java developer you definitely know who James Gosling is. For the RPG guys - see [https://en.wikipedia.org/wiki/James_Gosling](https://en.wikipedia.org/wiki/James_Gosling) :)

This panel demonstrated how they use NetBeans. One of the interesting demos was the integration with the new Oracle Cloud Dev environment.

# Being Productive with Maven, Java EE, and the Cloud (Adam Bien, Harshad Oak, Martijn Verburg)
Another panel discussion on how to develop Java EE applications using NetBeans and maven.

Some cool Maven plugins that also works well with Netbeans:

* [Jacoco](http://eclemma.org/jacoco/trunk/doc/maven.html) - code coverage library
* [Visualee](https://github.com/Thomas-S-B/visualee) - plugin to visualize java ee projects
* [Asciidoc](http://www.methods.co.nz/asciidoc/) and [Analyze](https://maven.apache.org/plugins/maven-dependency-plugin/analyze-mojo.html) - to create awesome JAX-RS documentation

I also had the privilege of meeting [this guy](http://blog.adam-bien.com/). (for the RPG guys, we watch his video series every Friday)
![session_4](/images/More_than_9_million_developers/session_4.jpg)

# James Gosling, Robots, the Raspberry Pi, and Small Devices
Panel discussion and some interesting talks on embedded development with Java and NetBeans. Most interesting, the robots that can follow everything from a great white shark to an oil spill in the middle of the ocean.
![session_5](/images/More_than_9_million_developers/session_5.jpg)

# Java Keynote
Everyone then gathered in one of the larger rooms in Moscone centre to hear the Java Keynote.

I also got to say hi to this guy:
![session_6](/images/More_than_9_million_developers/session_6.jpg)

Some other cool stuff during the Keynote. At some point, Oracle, IBM and RedHat were all on the stage ! They might be competing in the business landscape, but all working together to create better software. That is why Java is great !
![session_6b](/images/More_than_9_million_developers/session_6b.jpg)

Also - apparently there are more than **9 million Java developers** world wide (No **Trevor** - I am not sure who counted them), and more than 13 billion devices that run Java !

# Java on Your Phone!
We then had a quick lunch before heading into the next Panel discussion on different ways to use Java to create software on you phone. (Apart from the Java in Android application development)
![session_7](/images/More_than_9_million_developers/session_7.jpg)

* [Dukescript](https://dukescript.com/) - an open source library that allows you to create Software that can execute on Android or iOS or your Desktop (Linux, Mac and Windows)
* [Gluon](http://gluonhq.com/) - a library that brings [JavaFX](http://docs.oracle.com/javase/8/javase-clienttechnologies.htm) to your phone! Looks very interesting and promising.
* [Apache Cordova](https://cordova.apache.org/) - Hybrid app that uses HTML5, CSS and JavaScript and [Ionic Framework](All 3 options looks cool and worthwhile to play with.).

All 3 options looks cool and worthwhile to play with.

# Evolving Java EE (David Blevins, Oleg Tsal-Tsalko)
[David Blevins](https://www.youtube.com/watch?v=H5cO9MAvtzE) is one of my favorite speakers. He is one of the spec leads for many of the JSR's that makes up Java EE. Really good talk on where Java EE comes from and where it's going. Also an invite to the community to get actively involved in the specs. He showed us some cool stuff that we might have missed that is now in the latest spec.
![session_8](/images/More_than_9_million_developers/session_8.jpg)

# Building Nanoservices with Java EE and Java 8 (Adam Bien)
Adam Bien's sessions is always fully booked and a great talk. This talk ended up being mostly questions and answers, but still great. Adam demonstrated how simple and natural it is to create small, single responsibility, separate deploy-able units in Java EE. One of the question was also answered by just opening this wikipedia page:
![session_9](/images/More_than_9_million_developers/session_9.jpg)
(see [https://en.wikipedia.org/wiki/Cargo_cult_programming](https://en.wikipedia.org/wiki/Cargo_cult_programming))

At this point is was already 19h30 in the evening. Jam packed day ended off with a drink sponsored by NetBeans, Glassfish and Payara at [http://thirstybear.com/](http://thirstybear.com/)
