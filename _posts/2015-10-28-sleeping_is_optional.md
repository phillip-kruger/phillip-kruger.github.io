---
title: Sleeping is optional
tags: [JavaOne 2015]
bigimg: "/images/Sleeping_is_optional/4_swag.jpg"
image: "/images/javaone_logo.jpg"
aliases:
    - /2015/10/sleeping-is-optional.html
---
For the non technical readers, scroll to the pictures for some other interesting info.

Day 3. Last of the long 8am to 11 pm days. Because on day 4 we party!

# Journey's End: Collection and Reduction in the Stream API
Very interesting talk on the Stream API that is now available in Java 8 as part of Lambdas. Since we only recently moved over to Java 7, all of this is new to me. The scary thing is that they are already talking about the " in the early days of Java 8 " and most people are already using Java 8 in Production. So, we are far behind!

The Streams and Lambdas addition to the language allows you to do functional pieces inside your normal code. Once we move over to this we would again be able to delete more bloated code. This talk went into detail on what happens behind the scenes on some of these builds in functions (toList, toMap, groupBy, counting, maxBy, minBy) on streams.

![badge](/images/Sleeping_is_optional/1_badge.jpg)
*(your badge has a barcode that has all your details on. To enter a session your badge needs to be scanned to see if you have access. Preference is given to people that have booked.)*

# CDI and EJB alignment
Another very interesting talk by [David Blevins](http://www.tomitribe.com/blog/author/dblevins/). He went though the history of EJB and CDI and compared the two. Bottom line is that at this point they can do very similar things, but CDI is much newer and more extensible. With every version of Java EE, CDI becomes more prominent and will eventually replace EJB. This does not mean the death of EJB but rather the evolution as many concepts in CDI come directly from EJB.

One of the awesome things about CDI is that you can define your own scope, or in EJB terms, your own bean type. This gives you a lot of power.

We then went through some examples on how to create your own scope.

![screenshot](/images/Sleeping_is_optional/Screenshot_2015-10-28-05-26-13.png)
*(All your booked sessions, and some other info, are available on a mobile app.)*

# Servlet 4.0: HTTP/2 and Reactive Programming in Java EE 8
And yet another awesome talk on what's coming in the new version of Servlet. Most of the changes are actually due to the new version of HTTP. Yes, after 15 years, HTTP is getting a new version. As you can imagine, this is fairly significant as there is a lot of other frameworks and concepts built on HTTP.

HTTP 2 really solves all the workarounds that we need on the protocol in modern development, this will make many things simpler, cleaner and easier in the future.

A few examples:

* File concatenation and image sprites
* Domain sharding
* Inline assets like encoded images

will now be taken care of on protocol level.

One of the things in the Java world that would go on top of HTTP, is Servlets. So this upgrade of the HTTP protocol also means that Servlets can now upgrade.

That in turn will allow other frameworks and protocols built on top of Servlets to also upgrade. Think REST, SOAP, JSF etc...

This also means a new HTTP Client in Java SE that will be included in version 9
![vote](/images/Sleeping_is_optional/5_vote.jpg)
*(after every talk you can rate the speaker and the talk. This will influence what you see next year)*

# JPA in Reverse: Pushing Database Events to Java EE Applications in Real Time

> There are only two hard things in Computer Science: cache invalidation and naming things.
> -- [Phil Karlton](http://martinfowler.com/bliki/TwoHardThings.html)

This talk was 'suposingly' trying to give a solution for cache invalidation. The first part of the talk defined the problem and the current known solutions and this was the great and interesting part.
Current known solutions:

* **Expiry:** Easy to implement, but you still have a possibility of stale data.
* **Periodic refresh:** Better than above but still a change of stale data.
* **Triggered messaging:** Event driven and near real time, but hard (and ugly) to implement.
* **Database change notification:** Specific to Oracle database

The second part was a bit disappointing as their proposed solution is tied to a oracle product called Golden Gate, and from what I could gather still not really a better solution as what's already out there. The real-time in their solution is actually near real time.  The use of JPA in the talk title was also a bit misleading.
![rasp](/images/Sleeping_is_optional/2_rasp.jpg)
*(the voting stations are powered by raspberry pi !!)*

# Avoiding Big Data Antipatterns
Great talk by [Alex Holmes](http://hadoop%20in%20practice/) on the common pit fall when starting to look at Big Data. I am very new to this so I battled to always relate to the problem, but the speaker managed to still keep my attention.

Some of the things he mentioned that I need to go and read up on:

* [Kafka](http://kafka.apache.org/)
* [Casandra](http://cassandra.apache.org/)
* [HyperLogLog](https://en.wikipedia.org/wiki/HyperLogLog)

The take away for me was, do not believe the hype, you possibly do not need big data. But if you do beware of the vendors and the [big hammer syndrome](https://en.wikipedia.org/wiki/Law_of_the_instrument).
![mic](/images/Sleeping_is_optional/6_mic.jpg)
*(In some session rooms you can use you phone as a mic to ask questions)*

# What Would ESBs Look Like If They Were Done Today?
This talk by [Markus Eisele](http://blog.eisele.net/) did not give the answer to the above question. It just defined the problems related to traditional ESB's and why micro-services are now becoming more popular. There are still many things that we need to think about in the micro-services approach but the general take away is that it is evolutionary.

Micro-services is still a very loose definition and depending on who you ask you will get different answers.

The general feeling though,  is  that the centralization of concerns, like what an ESB does is maybe not a good idea.
![tshirt1](/images/Sleeping_is_optional/7_tshirt1.jpg)
![tshirt2](/images/Sleeping_is_optional/7_tshirt3.jpg)
![tshirt3](/images/Sleeping_is_optional/7_tshirt2.jpg)
**(you can make your own t-shirt)**

# Let’s Discuss MVC 1.0
I am very excited that this will now soon be a spec. I think this is something that was missing from the Java EE world.

Yes **Righard**, MVC is as old as the mountains, but this is not the conceptional MVC. This is potentially just a very bad name for the new Action based view technology that will be included in Java EE 8.

Up until now Java EE only had a component based view technology as part of the spec. Although JSF is also built on the MVC pattern, the controller is 'hidden' to the developer in JSF.

This new spec will allow you to build views more like you build REST endpoints and in fact the current RI just added some extensions to JAX-RS.

`@Controller` will now transform your REST service to a Controller. This can be done at method level allowing you to combine REST API and view. Awesome!

The RI is [Ozark](https://ozark.java.net/) and is already available for download.

# How to Thrive on REST/WebSocket-Based Microservices

The technical content of this talk was good, but this was yet another definition of micro-services where size (as in actual file size) and startup time mattered. As far as I am concerned these are not the real drivers of micro-services. So I think the title might have been a bit misleading.

Still very informative talk and demo on WebSockets.

![coffee](/images/Sleeping_is_optional/3_free_coffee.jpg)
*(did I mention the free cappuccinos ?)*

# Meet the Java Standards Leaders
A more non technical discussion on how the Java, or more specifically the [JSR](https://jcp.org/en/jsr/overview) ecosystem works. I found it very informative and will have to give you guys a more detailed feedback on this once I am back.

I think the reason that Java is still going strong after 20 years, is mainly because of how this works.

The JCP is an open standards body with:

* open process
* open mail list
* open issue tracker
* open test cases
* anyone can join
* free

The expert group of any specification has to deliver 3 things in the open:

* the spec
* the [RI](https://en.wikipedia.org/wiki/Reference_implementation)
* the [TCK](https://en.wikipedia.org/wiki/Technology_Compatibility_Kit)

This is one of the greatest things about Java. See [this list](https://jcp.org/en/jsr/all) of current JSR's.
