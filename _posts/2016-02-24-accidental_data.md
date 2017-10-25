---
title: Accidental data
tags: [IBM Interconnect 2016]
bigimg: "/images/Accidental_data/20160220_194214.jpg"
image: "/images/ibmconnect_logo.png"
aliases:
    - /2016/02/accidental-data.html
---
# Cultures, Practices and Tools that Support a DevOps Approach: An Open Conversation
This panel discussion made up of some industry experts shared some insights into creating a DevOps work environment.

Some notes from this discussion:

* Organisational structure is very important (Conway's Law)
* Smaller environments and smaller independent teams
* Physical space important - teams to sit together.
* Smaller, more frequent deploys into Prod environment
* Start considering cloud as a way to create quicker infrastructure on demand
* Design thinking - quicker feedback from users
* Embrace failure.
* Culture, practices and tools.
* Roles starts blurring, start hiring general engineers that can fulfill all roles
* Administrator role replaced by automation
* You might hurt some feelings along the way.
* Evolutionary architecture approach. No big upfront decisions and design.

# Presence, Personality and Pulse
See similar talk here [https://developer.ibm.com/bluemix/2015/12/01/presence-personality-pulse-at-startechconf/](https://developer.ibm.com/bluemix/2015/12/01/presence-personality-pulse-at-startechconf/)

Definitely my favorite talk of the conference so far. [Anton McConville](https://twitter.com/antonmc) gave us a fresh view of the possibilities that IoT are bringing and how it will change the way we interact with out clients.

Computers have always just done 3 things:

input -> process -> output

The only real difference now is the sources for input and output are exploding and this is creating a lot of, what he calls "accidental data".

And it's this accidental data that we need to use to understand our customers better.

He then went through some use cases and examples for:

* **Presence** - so location based. Knowing where people are. The example used iBeacon technology and some hardware from http://estimote.com/ . This is what we are doing in India. And need to at home.
* **Personality** - This example used Watson and public profiles of users to understand more about their personality.
* **Pulse** - Example of how Watson can understand emotions in a voice and decide the mood of emotional state of the person.

![accidental_data](/images/Accidental_data/20160223_100932_HDR.jpg)

# What is new in IIB
This talk touched on some of the enhancements in the v10 release of IIB, including upcoming V10.0.0.4 (March 2016)

* Move to the cloud
* Docker support
* Built in test environment
* REST Api
* Callable flow to get from Cloud to on premise (or cloud to cloud)
* Extreme Scale cache built in and accessible from mapper

![iib](/images/Accidental_data/20160223_131555.jpg)

# Traditional WAS vs Liberty (or should you be using Liberty)

Short answer (for us). Yes.

This talk went through the features of traditional WAS and Liberty.
Some things that I found interesting:

* Easier to do DevOps, Microservices and cloud with Liberty
* Better internal library hiding in Liberty. No more classloader issues.
* Easier to plug and play implementations of specifications in Liberty
* Java 8 and Java EE 7 support
* Dynamic configuration
* Better integration with build and deploy automation tools

# What's new in ODM

* This talk went though the improvements to ODM.
* Move to cloud
* Easier to apply changes without a restart
* Better GUI's
* Enterprise console to move to business console.
