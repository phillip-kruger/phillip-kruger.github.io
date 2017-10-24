---
date: 2016-02-25T20:04:40.407Z
title: Rocket man
tags: ["IBM Interconnect 2016"]
image: "images/Rocket_man/20160224_185720.jpg"
share: true
authorlocation: "Las Vegas, NV, USA"
aliases:
    - /2016/02/rocket-man.html
---
The 4th day of the conference was a short one, as the [Elton John](https://www.youtube.com/watch?v=uNNl3C0qvKg) concert started at 6, all the sessions ended at 5:30ish.

# Agile Development using Java EE7 with WebSphere Liberty
This lab took us through the development of a basic system and pushing it to the cloud. Very basic but still worth it. At least confirmed that we are on the right track in terms of our way of developing.

# Microservices: Where do they fit within a Rapidly evolving Integration Architecture
Very good conceptional talk on Microservices and how / where they fit in the bigger picture, with some comparison to SOA.

Some interesting notes:

* ESB is a pattern not a product, and the ESB still exists, it's just not visible (and a fiscal layer) as in SOA, but conceptionally it's there.
* In microservices, look at using [kafka](http://kafka.apache.org/) for messaging
* To reduce dependencies between services you might see an increase in duplication. It's OK.
* In Microservices, Service Discovery is more like DNS than like UDDI
* Think more about the ownership boundary than the physical boundary. Hybrid on-premise and cloud is the future.
* Decentralize integration. Each service does it's own integration boundary
* Your application developer can now do your integration (that is how simple and easy it became)
* Think about tagging rather than categorizing when grouping services.

When you design a service:

* Who owns it ?
* How long does it last ?
* How often does it change ?

# Free your Administrative Spirit with the Liberty Admin Center
Another very interesting talk on the new Admin Center for Liberty. Much cleaner and more configurable than the old Admin Console of TWAS we all love to hate.

Yes. TWAS is what we now call the Traditional WAS. The crap one :)

The new Admin Center is totally pluggable, so not only do you just plug in the parts you care about, but you can also write your own plugins. (Like maybe one for our properties ?)

It is:

* Lightweight
* Task orientated
* Built on a public API (so you can hook your scripts in)
* Always in sync with what you do on the cmd line. (via Websockets)
* Support for Java EE and Node/Strongloop
* Support for docker containers (so stop / start your container)
* Collective controller to control thousands of servers
* Can be personalized per user
* apiDiscovery feature (more on that later)

# dev@
![dev_at](images/Rocket_man/20160224_152900.jpg)

I then spent some time a the dev@ section where you can play with new technologies. I wrote a quick app that uses [IBM Watson](https://developer.ibm.com/watson/) API. I detected a security breach with [IBM X-Force](http://www-03.ibm.com/security/xforce/). I did some analysis on my golf swing with some awesome IoT gadget. Apparently I should go Pro ...

# The future of work
Very interesting talk on how Social and IoT are changing the way we are going to work in the future. I did not know this, but IBM has a stack of tools in this area. A few years ago your business competitor was clear. Today you can not see it, it's not your natural enemy.
see [http://www-03.ibm.com/software/products/en/ibm-connections-cloud-s1](http://www-03.ibm.com/software/products/en/ibm-connections-cloud-s1)

![work](images/Rocket_man/20160224_160102560.jpg)

# Using IBM WebSphere Liberty and Swagger to Make your Services Accessible
Presentation available [here](https://drive.google.com/open?id=0B_k6LNFyOaDHRVIxMjVOSFJpVDA)

Very good talk on the new apiDiscovery feature in Liberty. This allows liberty to discover all your REST endpoints and automatically create the swagger documents for you. It also ships with Swagger UI to make this available as a web page for testing and human consumption. This is really cool and one of the reasons we need to start switching to Liberty.

# Elton John
Then we all went to Elton John concert. I am not a big fan, but the food and drinks were good.
![work](images/Rocket_man/20160224_195642_Pano.jpg)
