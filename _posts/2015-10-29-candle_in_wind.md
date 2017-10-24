---
layout: post
title: Candle in the wind
tags: [JavaOne 2015]
bigimg: "/images/Candle_in_wind/IMG_20151027_081551.jpg"
aliases:
    - /2015/10/candle-in-wind.html
---
*Some photos from the [Oracle appreciation event](https://www.oracle.com/openworld/appreciation-event.html). There was a guy there going on about some candle in the wind? That does not sound sustainable, but anyway...*

#  Java EE Connectors: The Secret Weapon Reloaded
This talk went through the JCA history and the planed changes / enhancements in the pipe line. MDB (the way we listen for messages on a queue) are built on JCA.

We then went through some examples to demonstrate the power of JCA.

For the guys back home - we should be building our connection wrapper to AS400 (the old socket one) on this.

Some of the examples included a JCA connector that allows you to connect to the server via ssh. Another one that can listen for some tweets and pull them into the system.

The examples are available on [https://tomitribe.io/projects](https://tomitribe.io/projects)

# How Netflix Thinks of DevOps—Spoiler: It Doesn’t.
![netflix](/images/Candle_in_wind/netflix.jpg)
Best talk at the conference (so far). [Dianne Marsh](http://diannemarsh.com/) shared some inside info on how Netflix run their shop. Netflix's operating cost is one of the lowest, and that is all due to their culture and their organisational structure. (Very similar to [what I have proposed]({{< relref "Guerrilla_programming.md" >}})...)

Some context: Netflix has

* 100's of microservices
* 1000's of daily production changes
* 10000's of instances
* 100000's of customer interaction
* 100000000 of metrics
* but... **only 10 operations people.**

They get this right because they have the following rules:

* **You build it, you run it:** Their teams are small (6 - 10) teams consisting of senior developers (they do not hire juniors).
* **You are on call:** The 10 operations people are only facilitating. The team is responsible for supporting their service(s) in production.
* **Freedom and responsibility:** These teams have freedom to build the services they are responsible for in any language and using any framework. If your service depends on another service, and that service is down, it is not an excuse for your service to be down (fault tolerant).
* **They also test in production:** They have a few monkeys that they let loose on their production system once a month.
* **Chaos monkey:** Some program that randomly kills services and networks and so on, simulating possible scenarios that can happen on a server.
* **Chaos gorilla:** Some program that can bring down a whole server.
* **Chaos Kong:** This program can bring down a whole data center.

All of this happen while millions of people watch movies via Netflix! They do not even know this is happening.

# Real-World Batch Processing with Java EE
This talk was on [JSR 352](https://blogs.oracle.com/arungupta/entry/batch_applications_in_java_ee) that is now part of Java EE 7. Not a great talk, dragged on a bit long about the history of batch and never really got into the detail of the JSR. Still good to know that this is available...
![elton1](/images/Candle_in_wind/elton_1.jpg)

#  Introduction to modular development
Building on Preparing for JDK 9, this talk went into more detail on the modular changes coming in Java 9.

Basically we will soon have the concept of a module, that is really very similar to a bundle in OSGi, except it will enforce the dependencies on compile time already.

You will declare your dependencies, and your boundaries, inside a new file called module-info.java.

**public != accessible**
This means that public methods do not necessarily mean accessible anymore.

We went through some of the new tools that will be available to assist with this. Look at jdepend and jlink.

I think this is an awesome change, but it seems there are some mixed feelings from the community about this.
![elton2](/images/Candle_in_wind/elton_2.jpg)

# EJB 3.2/JPA 2.1 Best Practices with Real-Life Examples
This talk went through some new things in JPA. (JPA is the abstraction layer that we use to connect to a data source)

**@Converter** - a new way to intercept data on field level just before we persist and just after we read.

This allows you to do things like encryption/decryption and other data manipulation.

JSR 310 (The new **Date Time API**) was released after the new JPA spec so the new date objects are not included as mappings to JPA fields. You can use above @Convert to do the mapping yourself.

**Lazy Loading** and the new entity graph. This example went though some named entity graph and some dynamic entity graph examples.

Some new enhancement in **JPQL**. The ON and FUNC keywords.

The new **Bulk update** via the Criteria API.

Built in support for **stored procedures**.
![elton3](/images/Candle_in_wind/elton_3.jpg)

# The Java EE 8 Opportunity
One of the best technical talks, here we went thought the affect that the new language features in Java 8, most notably **default** and **Lambdas**, could have on the Java EE API.

Very similar  to what has happened after the language added generics and annotations, this will bring a huge change in the API.

The biggest conceptional change is that in Java 8 logic is mobile. This means that a lot of declarative annotation based things can now be movable and where the producer was the only one previously that can dictate behaviour, this can now also originate from the consumer. Example, the consumer might decide to call a method in an asynchronous manner, rather than the producer exposing this asynchronously declarative.

We then went through some examples on what this might be and how this might look like.

Some other really cool things that came up in this presentation is the ability to multicast. So you can cast an Object to any of it's interfaces.
~~~java
(Serializable & SomeInterface)someObject;
~~~
![elton4](/images/Candle_in_wind/elton_4.jpg)
