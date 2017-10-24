---
date: 2016-02-23T20:04:40.407Z
title: It's cold in the shade
tags: ["IBM Interconnect 2016"]
image: "images/It_cold_in_shade/20160219_175340%2B%25281%2529.jpg"
share: true
authorlocation: "Las Vegas, NV, USA"
aliases:
    - /2016/02/it-cold-in-shade.html
---
For the morning of day two I spent most of my time in the Open Lab. This is a room with PC's already setup to "play" with certain tools / technologies. You can help yourself to mini courses and there are people around to answers questions and assist.

I played with IBM Mobile first, not really that impressive. I am not sure if there are any real benefits, other than just using Cordova. I also spent some time with [Docker](https://en.wikipedia.org/wiki/Docker_(software)), Liberty and [IBM Bluemix](https://en.wikipedia.org/wiki/Bluemix). All very cool and definitely something we need to look at.

All of the Lab stuff is in one building - The MGM Grand as seen in the photo above. It's a bus drive to the other venue (Mandalay Bay) and due to the time it took (and the queue) I missed my next session (IBM BPM Best Practices). That sucks. Maybe I'll do some BPM in the Lab if I get a chance. (The Lab below)
![microservices](images/It_cold_in_shade/20160222_101839.jpg)

# IBM DataPower Gateway: Common Use Cases
Datapower is an impressive piece of software. It feels like there is not much you can not do with it. But as with any thing that can do everything it does feel daunting sometimes. You are going to need a real datapower expert to get the most value for your money.

It also means that it's not something you just put down and it works. It's heavy and centralized.

I am still in two minds about it. I think in a distributed, microservice world, datapower is just in the way. But, as always, I might be wrong...

Anyway, the talk took us through some use cases where datapower assists in solving certain problems. What bugs me though is that solving problems with a tool means you need the tool to solve the problem :)
So it's a commitment to the tool. Also called [Vendor lock-in](https://en.wikipedia.org/wiki/Vendor_lock-in).

# Expo
Myself and Sibu then spent some time at the Expo. Had some very interesting chats. Here are some of mine that I need to google when I get home:

##  JKool
[https://www.jkoolcloud.com/](https://www.jkoolcloud.com/)
Monitoring (so New Relic) but with the ability to span the Java world into AS400, MQ and other technologies. No more - "we did put the message on the queue" , "prove it" arguments.
## UrbanCode
[https://developer.ibm.com/urbancode/products/urbancode-deploy/](https://developer.ibm.com/urbancode/products/urbancode-deploy/)
This is what we need to become more Agile and implement DevOps. Move away from our custom scripts that do the deployments and move to this automated deploy tool that can also enable [blue-green](http://martinfowler.com/bliki/BlueGreenDeployment.html) deployment.
## Cloudsoft
[http://www.cloudsoftcorp.com/](http://www.cloudsoftcorp.com/)
Similar to UrbanCode, except they can move the application to any cloud platform (or on premise) Built on [Apache Brooklyn](http://brooklyn.apache.org/)
## CloudOne and IBM BlueBox
Both give you a private cloud that can even be hosted on premise. They manage the environment remotely.
* [http://oncloudone.com/](http://oncloudone.com/)
* [https://www.blueboxcloud.com/](http://oncloudone.com/)
## IBM Bluemix and beacons
Nice demo (and I am also going to a talk on this) on what exists, and how simple and easy it it to create applications that can detect where people are based on beacons.

See [http://www.ibm.com/developerworks/library/mo-bluemix-ibeacons/index.html](http://www.ibm.com/developerworks/library/mo-bluemix-ibeacons/index.html)
