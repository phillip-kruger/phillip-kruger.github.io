---
title: Open by design
tags: [IBM Interconnect 2016]
bigimg: "/images/Open_by_design/20160220_144935.jpg"
image: "/images/ibmconnect_logo.png"
redirect_from:
  - "/2016/02/open-by-design.html"
---
Day 1 of [IBM Interconnect 2016](http://www.ibm.com/cloud-computing/us/en/interconnect/) kicked off with a bang. Back to back sessions from the Research Lab Team and ending with the [Open Tech Summit](http://www.ibm.com/cloud-computing/us/en/interconnect/agenda/schedule/specialevents/)

# Future directions in Enterprise Mobile Computing
Good talk on what IBM Research is doing in the IoT (Internet of Things) world. Something interesting the presenter said:
"The mobile revolution is not an IT trend, but rather a business trend"

We still have not seen the business revolution that IoT is bringing. In the future all business will make use of IoT in some way.

* Glasses (think safety glasses in a factory)
* Watch
* Earpiece
* Temperature

All working together  with other sensing capabilities of the workplace to feedback and assist in the workplace

Context is important, and this is where IoT can help.

The systems that we will build in the future will have to deal with vast amounts of unstructured data, and have the ability to make sense of it.

Some demos as [https://wearables.mybluemix.net](https://wearables.mybluemix.net)

# Wearables in the Enterprise
50% of people already use wearables. This will increase drastically over the next few years and soon people will have more than one.

Customers will expect business to engage with them via these wearables.

He then showed some demos on what IBM is doing in the workplace safety field using IoT. Think hardhats and gloves and shoes all connected to the network...
Sensors like location, temperature, noise, heart rate, gas, accelerometer all feeding back to create a safer workplace.

Another demo done using IoT to feeback stats of a pregnant woman. Recognizing gestures to determine if you are drinking water etc.

With all this information about the customer now available, privacy becomes a big issue.

# Cognitive IoT - Today, Tomorrow and beyond

* Many of the capabilities exist already
* Analyzing the data is the problem
* Getting insights into the data is the challenge
* Security becomes a big concern
* The huge amounts of unstructured data volumes is a challenge

88% of the data is dark (unusable).

Watson can help with the uncovering of this data and provide predictive and prescriptive analytics.

> Cognitives are not programmed, it's learned from input and collect, and Watson API allows business to build such systems

# Cloud Programming model

One of the research projects at IBM focusses on a tool called Whisk that allow you to easily create manipulation of existing API's for use in the mobile / IoT space. Adding filtering and piping API together to not only hide the changing API from your consumer, but create a custom API that you need.

Whisk is not available to the public (research effort) and they do not know (yet) in what form it will be made available. At this point it creates Swift and Node and has a few standard API integrations (like to weather.com and watson etc).

Piping can be sequential or parallel and whisk auto scales in the cloud.

# Containers - Changing the game in the cloud

Very interesting talk on how containers on bare metal are changing the visualization in the cloud. Running the containers on bare metal gives much more visibility of the environment and allow for better monitoring and analytics.

IBM Bluemix has spend a lot of effort getting this right in the cloud.

# Microservices - The good the bad and the ugly
![microservices](/images/Open_by_design/20160221_130722892.jpg)

Very basic overview of the microservices landscape. Nothing new really.

Good:

* Bounded scope
* Agility
* Scaling
* Resilience
* Polyglot

Bad:

* Distributed adds management overhead
* Framework functions like discovery, routing, configuration
* Communication penalize performance

Ugly:

* Delivery pipeline
* Versions
* Right separation
* Transactions

# The efficient API economy
![api](/images/Open_by_design/20160221_134804489.jpg)

This talk showed how API Harmony can help with navigating the API landscape. IBM is crawling all open API's and builds a graph of usable API's for easier discovery.

# Cloud Native DevOps

* The move to the cloud is driven by the quest for agility.
* Cloud native principals is proven at large scale (Netflix, Amazon etc)
  * Loose coupling
  * Design for resilience
  * Instrumentation
* Change management creates the bottleneck.

The combination of tools and a enterprise architecture team should solve this.
Org structure is important. Small, empowered teams around a business case.
Active deploys needs stateless microservices. Auto rollback determined by metrics.

> Testing is now done is PROD !

# Open Technology Summit

IBM is committed to open source and open specifications. We heard from 13 industry leaders on different fields where IBM is involved in open source and/or open specifications.

* OpenStack
* Swift
* IBM Blue Box
* Cloud foundry
* IM Container Services
* Blockchain
* IBM Bluemix garage method
* Elastic
* Spark and Kafka
* ODPi
* Swagger
* Node
