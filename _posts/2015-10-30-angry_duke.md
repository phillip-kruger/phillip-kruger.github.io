---
title: Angry Duke
tags: [JavaOne 2015]
bigimg: "/images/Angry_Duke/angry_1.jpg"
image: "/images/javaone_logo.jpg"
aliases:
    - /2015/10/angry-duke.html
---
Last day of the conference. Photos are from the Java Community Keynote.

#  Distributed Streams: The Stream API on Steroids
Very advanced but very good talk on how Coherance had to change the implementation of the new Stream API so that it can work in a distributed world.

This essentially gives us distributed map/reduce and can be compared to a Hadoop.

Stream: to define a pipeline of zero or more intermediate operations and a singe terminal operation.

Because Coherence store entries of a map on multiple servers, doing the above is not that simple (but they did it)

Next they want to look at keeping your stream function up to date in real time ! This sounds awesome - real time operations on huge data sets...
![angry_2](/images/Angry_Duke/angry_2.jpg)

# Reactive Java EE: Let Me Count the Ways!
Reactive is one of the new buzz words floating around. This talk demonstrated how Java EE already have all the building blocks to develop reactive programs.

The properties of a reactive system:

* Event driven
* Asynchronous
* Non-blocking
* Message driven

Some other properties that we do not need to explicitly code because they are taken care of by the application server are:

* Responsive
* Resilient / fault tolerant
* Elastic / adaptive
* Scalable

We then went though example showing:

* Message Driven Beans
* Asynchronous Session Beans
* Events and Observers
* Asynchronous Servlets and NIO
* Asynchronous REST Service and Client
* WebSockets
* And the new Concurrency Utils

![angry_3](/images/Angry_Duke/angry_3.jpg)

#  Java Community Keynote

We all then went to the Java Community Keynote were we had to help the JUG leaders to find out why duke are so angry in the future (they build a time machine).

They had to travel through time to get the clues, and eventually figured out that **kids are the future**.

On the Saturday before we started, there was a JavaOne4Kids event where kids could spend the day at the conference and learn about Java.

![angry_4](/images/Angry_Duke/angry_4.jpg)

#  Java 8 in Anger

I think this could be a good talk, but it was delivered via Skype as the presenter could not be here. This spoiled it a little bit as it was hard to follow.

We went though an example on how to use some of the Java 8 features in Java SE and Java FX.

# Zero-Downtime Java Deployments with Docker and Kubernetes

Very good talk that took us though an 8 step setup of Kubernetes to enable full blown cluster setup with fail-over and load balancing and then add a zero downtime automated deploy.

This only work effectively in a stateless system, so we would have to make some code changes, but imagine we can go to production during the day without impacting the client ?

![angry_5](/images/Angry_Duke/angry_5.jpg)
