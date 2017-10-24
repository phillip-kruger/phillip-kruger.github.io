---
date: 2015-10-27T20:04:40.407Z
title: Coffee included
tags: ["JavaOne 2015"]
image: "images/Coffee_included/day_2_b.jpg"
share: true
authorlocation: "San Francisco, CA, USA"
aliases:
    - /2015/10/coffee-included.html
---
Our day started with a free coffee from Duke's Cafe:

There are coffee (and beer) stations all over the place here. You never have to be without caffeine :)

Here is a quick update of my loooong (8 am - 11 pm) day. You can also see some live streaming [here](https://www.youtube.com/watch?v=6exFuFJhfcA)

#  Java EE 7 in Action by [Reza Rahman](http://www.rahmannet.net/)
A nice (and long) session going through actual code examples of the new stuff in Java EE 7. (We are still on 6, but it's nice to see what's coming)

The example we went through is also available online for e-study:

* [https://java.net/projects/cargotracker/pages/Home](https://java.net/projects/cargotracker/pages/Home)

Here are some highlights on the updates in version 7:

* [JMS](https://en.wikipedia.org/wiki/Java_Message_Service) (update) to include delayed send, async send (for reactive programming) with the ability to register a callback. New fluent style API. Disable some default headers like message ID. (More in depth detail on the session later in the day on What’s Coming in JMS 2.1)
* [WebSockets](https://en.wikipedia.org/wiki/WebSocket) (new) to have both client and server as part of the spec. This essentially replaces long polling (see [comet](https://en.wikipedia.org/wiki/Comet_%28programming%29)).
* [JSON Processing](https://javaee.github.io/jsonp/) (new) is the abstraction of json processing, so we decouple from the underlying implementation similar to JAXP for XML.
* [Bean validation](http://beanvalidation.org/) (upgrade) to now be included in JAX-RS and WebSockets. Adding method validation and custom validation using @Inject. Also allow expressive messaging via [EL](https://docs.oracle.com/javaee/6/tutorial/doc/gjddd.html) 3.
* [JAX-RS](https://en.wikipedia.org/wiki/Java_API_for_RESTful_Web_Services) 2 (upgrade) Spec now includes the client API. New message filters and interceptors. Async support. Content negotiation with new qs (quality service) parameter to assign weight. Hypermedia support.
* [JPA 2.1](https://en.wikipedia.org/wiki/Java_Persistence_API) (upgrade) now has schema generation as part of the spec. More support for stored procedures. Entity graphs to allow creating partial populated objects (so finer grained than just LAZY or EAGER loading)

Also see the [Aquarium blog](https://blogs.oracle.com/theaquarium/) for update on the Java EE spec

![img_2a](images/Coffee_included/day_2_a.jpg)

# Prepare for JDK 9
This talk went in detail through the disruptive changes in JDK 9. Mostly w.r.t the new modularity framework to break the JDK up into modules.
Really exciting stuff that is coming. We might see less classpath issues in the future :)

For more info see [Project Jigsaw](http://openjdk.java.net/projects/jigsaw/spec/) (JSR 376)

Some other changes include the removal of some internal classes. You can use the jdeps tool to check your code base for usages of these. Most affected will be:

* sun.misc.BASE64Encoder and Decoder
* sun.misc.Unsafe

There will however be command line flags to allow for backward compatibility.

There is also now an API to access the run-time (vs just looking at rt.jar) as the JDK is now made up of modules and your run-time can be a subset of all modules.

Another interesting change is the removal of ext in the JDK. Extensions will now be done through modules.
![img_2c](images/Coffee_included/day_2_c.jpg)

# CDI 2.0: What’s in the Works?
This talk from the spec leads of [CDI](http://www.cdi-spec.org/) took us through the enhancements they are planning for CDI.

CDI 2.0 (or maybe even 2.1) will be aligned with the Java EE 8 release.

Some of the notable enhancements:

* Async support for CDI Event. Because you will lose the context, this will be a new method on the interface.
* Potentially splitting up the spec into 3 parts (CDI Core, CDI for Java SE and CDI for Java EE)
![img_2d](images/Coffee_included/day_2_d.jpg)

# invokedynamic for Mere Mortals
I had an hour before the next session started so I popped into the exhibition hall to have a quick look. Apart from getting to talk to some awesome people there is the added benefit of free swag :)

Invoke Dynamic is a JVM feature typically used by people writing languages on top of the JVM. This talk explained the feature for "normal" developers.

For a list of languages that run on the JVM see [this link](https://en.wikipedia.org/wiki/List_of_JVM_languages)

Some interesting takes from this talk is that we can potentially use part of this in place of normal java reflection. Not only is this faster than reflection (due to caching of the linkage) but also easier to use.
![img_2e](images/Coffee_included/day_2_e.jpg)

# What’s Coming in JMS 2.1
This talk from the [spec lead of JMS](https://java.net/projects/jms-spec/pages/Home) took us through the proposed enhancements in the new version of the spec. Unfortunately we are far behind here. Due to only using Jave EE 6 we are still using JMS 1.2 :(

But that just means we will soon be deleting a lot of code - and I like doing that !!

As with all Java API and spec, we (the community) have access to take part in the definitions of these specs. He encouraged us to look at the early draft of the spec and contribute suggestions.

* Alignment with CDI Events.
* Even simpler annotations.
* Better fluent programmatic API
* Multiple callbacks
* Improved Message Beans (even less code needed) with some nice default for common use cases.
* Standardization of some common JMS attributes (that results in new annotations and less code)
* Batch support

![img_2f](images/Coffee_included/day_2_f.jpg)

# High-Performance Java EE with JCache and CDI
Basic but still cool talk on [JCache](https://blogs.oracle.com/theaquarium/entry/jcache_is_final_i_repeat) in Java EE. Simply adding @CacheResult to a method can cache the data to improve performance. In this example the cache was backed but Hazelcast and was nicely distributed and elastic. They demonstrated adding and removing of caching nodes and the automatic re-syncing of the data.

`@CacheRemove` is also available for removing of cache but it seems like the expiring of cache via some strategy is still limited in the spec and only defined time based strategy. Most implementations however support more strategies like size and count based.

Other options include some callback listeners to then manually expire cache.
![img_2g](images/Coffee_included/day_2_g.jpg)

# Most Popular Java (EE) Q&A: Airhacks.tv Live

This was a live recording of Adam Bien usual [Airhacks](http://airhacks.com/) Q&amp;A session.  It should be available online soon. I will then update the post with a link to the video.
Update: [https://www.youtube.com/watch?v=cWD0a7f3YxY](https://www.youtube.com/watch?v=cWD0a7f3YxY)
![img_2h](images/Coffee_included/day_2_h.jpg)

# High Availability with Java EE Containers, JDBC, and Java Connection Pools
Panel discussion by the developers of the Oracle JDBC drivers. This was interesting but not really applicable to what we are doing (at the moment). Because we usually use OR mappers to access data, we are hidden from this lower level detail. Still interesting to see what is happening under the covers.
![img_2i](images/Coffee_included/day_2_i.jpg)

# Developing a Nonintrusive Annotation-Based Java Monitoring
This was a little bit of a disappointing talk. Very basic and kind of old. We already use newer technology in some of the new code.

See `@Trace` and `@NotifyAnd**` annotation in our code base.
