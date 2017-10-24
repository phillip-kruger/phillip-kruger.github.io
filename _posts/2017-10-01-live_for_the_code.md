---
layout: post
title: Live for the code
tags: [JavaOne 2017]
bigimg: "/images/Live_for_the_code/banner.jpg"
---
# Meet Apache Netbeans

We had a sneak peak at some of the Apache Netbeans features last night with the informal Netbeans get together at the [thirsty bear](http://thirstybear.com/), when [Geertjan Wielenga](https://twitter.com/GeertjanW) gave us a quick demo of (the very impressive) [Oracle JET](http://www.oracle.com/webfolder/technetwork/jet/index.html).

![](/images/Live_for_the_code/oracle_jet.jpg)

This session gave us a quick history of Netbeans, and the future plans for it. Especially now that it's an [Apache](https://netbeans.org/community/apache-incubator.html) project.

Oracle is still invested in making this a success as many of their own tools are built on Netbeans. However, other big companies (example Red Hat, Airbus, Boeing) are also building solutions on top of the Netbeans platform and can now contribute to the development and road-map.   

We also got shown a quick demo of Netbeans 9, running Java 9 and the tool support for Java 9 features like JShell and Modules, with a cool visual representation of the module graph.

![](/images/Live_for_the_code/netbeans.jpg)

### Read more:

* https://jaxenter.com/netbeans
* https://netbeans.org/community/news/events.html

# Successful Java EE DevOps in the cloud

*"Software developers are the most important people in the world"*

This talk by [Edson Yanaga](https://twitter.com/yanaga) gave us some background on DevOps, and more specifically DevOps in the cloud.

![](/images/Live_for_the_code/devops.jpg)

Cloud vendors might try and lock you in with some extra services like Pipeline automation, Blue Green Deployments etc, making the cost of change too high.

*"Outside innovation is always bigger than inside"* and this is why Open Source is becoming the default standard for software.

Containers and Container Orchestration will also soon become the de-facto standard.

[Kubernetes](https://kubernetes.io/), as an example, allows you to change cloud providers in minutes.

### Istio

An open platform to connect, manage, and secure microservices

https://istio.io/

* Intelligent Routing and Load Balancing
* Resilience Across Languages and Platforms
* Fleet-Wide Policy Enforcement
* In-Depth Telemetry and Reporting

# 5 Pillars of a Successful Java Web application

![](/images/Live_for_the_code/pillars.jpg)

Very interesting talk on some pillars (or principles) to create a sustainable web app in Java.

[Eder Ignatowicz](https://twitter.com/ederign) and [Alex Porcelli](https://twitter.com/porcelli) from Red Hat took CDI to the browser, allowing you to code with CDI Annotations and bind that to HTML. Very interesting concept. It decouples the code very nicely and abstracts the underlying implementation. This allows you to change to (whatever the latest JavaScript library is) with parts of your system, while other parts stay in the older technology.

![](/images/Live_for_the_code/pillars2.jpg)

They also brought CDI Events to the browser, underlying using a Bus, Long polling, SSE and WebSockets.

Look at http://erraiframework.org/

# Building a recommendation Engine with Java EE

[Otavio Santana](https://twitter.com/otaviojava) and [Hilmer Chona](https://twitter.com/hchona) explained why recommendations are next in our web journey ([Web 3.0](https://en.wikipedia.org/wiki/Semantic_Web#Web_3.0)). Context matters. And for us to store the information in a way for us to get this context easy, traditional relational DB might not be the choice.

![](/images/Live_for_the_code/graph.jpg)

However, graph databases do not have a standard, and this means you lock yourself into the implementation, and this is where [Apache TinkerPop](http://tinkerpop.apache.org/) and [JNoSQL](https://github.com/eclipse/jnosql-artemis) can help.

![](/images/Live_for_the_code/graph2.jpg)

# Microservices Data Patterns: CQRS and Event Sourcing

[Edson Yanaga](https://twitter.com/yanaga) and [Víctor Orozco](https://twitter.com/tuxtor) gave a high level talk on the strong eventual consistency model using [CQRS](https://martinfowler.com/bliki/CQRS.html) and [Event Sourcing](https://martinfowler.com/eaaDev/EventSourcing.html). Basically this means that your data might be outdated for a moment (but never wrong).

![](/images/Live_for_the_code/cqrs.jpg)

Event sourcing (or the streaming of events) is then basically the storing of changes, rather that the actual updated date. This means you can get to the current value by re-playing the changes (this might be something that you can cache, as the replay might be slow).

When implementing this you need to think about

* Messages (not messaging) between services, rather than Remote calls between services.
* Update frequency
* Update size
* Staleness
* Time to build CQRS data
* Change notifications

We (Multiply) need to do this for User Events and User Scores.

Some projects we need to look at:

* [Teiid](http://teiid.jboss.org/) (Data virtualization)
* [Debezium](http://debezium.io/) (DB streaming changes)
* [Infinispan](http://infinispan.org/) (Caching)

*We move code rather than data*

**This was my favorite talk of the day :)**

# Powerful Lessons from Top Java EE Experts

This was a discussion panel with some of the Java EE Industry experts where the audience could ask question.

![](/images/Live_for_the_code/experts.jpg)

This discussion was mostly around
* the recent [open sourcing of the Java EE spec](https://blogs.oracle.com/theaquarium/opening-up-java-ee),
* the possible re-branding of Java EE ([EE4J](https://projects.eclipse.org/projects/ee4j/charter))
* the move to the eclipse foundation
* the community involvement

Some notes:

* For this to work we will need strong community involvement
* The vendors (Oracle, Red Hat, IBM) are still heavily involved
* This should be easier to contribute (GitHub Pull request)
* The way implementors do the TCK and documentation will have to be standardized
* We need to lookout for fragmentation
* EE4J is the project name, not necessary the brand

All and all, this is a long overdue positive step and I believe we will see the Java EE specs moving faster now.

Some of the experts on stage:

* [Adam Bien](https://twitter.com/AdamBien)
* [Ed Burns](https://twitter.com/edburns)
* [Reza Rahman](https://twitter.com/reza_rahman)
* [Antoine Durand](https://twitter.com/antoine_sd)
* [Steve Millidge](https://twitter.com/l33tj4v4)
* [Edson Yanaga](https://twitter.com/yanaga)
* [David Heffelfinger](https://twitter.com/ensode)
* [Ruslan Synytsky](https://twitter.com/siruslan)
* [Ivar Grimstad](https://twitter.com/ivar_grimstad)
* [Kevin Sutter](https://twitter.com/kwsutter)
* [Bruno Souza](https://twitter.com/brjavaman)

(Go follow them on twitter)

# REST Services with the Play Framework and a security level with JWT

![](/images/Live_for_the_code/play.jpg)

[Mercedes Wyss](https://twitter.com/itrjwyss) showed us how you can (very easily) use the [play framework](https://www.playframework.com/) (that one traditionally links to view technology) to create REST Services. The nice thing is you can choose Java or Scala when implementing this.

Very interesting, and if you are already building your Web app in play it might make sense. It's very similar to [Jersey](https://jersey.github.io/) and Jersey MVC.
