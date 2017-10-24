---
date: 2016-05-05T20:04:40.407Z
title: Monkey methodology
tags: ["Agile", "Lean"]
image: "images/Monkey_methodology/monkeys2.jpg"
share: true
authorlocation: "Centurion, South Africa"
aliases:
    - /2016/05/monkey-methodology.html
---
The PM becomes a scrum master.

MS Project (or something similar) is replaced by a Jira account.

Dev teams have stand-up meetings every day.

**Hooray!** We have done it! We are now agile.

Agile. Possibly the most misunderstood methodology in big corporate companies today.

I think the problem, as with many things in a big corporate, is that growth/progress does not happen naturally. It's orchestrated with new teams, more people and more money.

So when we decide to "go agile" the first thing we do is to get a consultancy company in to help transition the old waterfall team into an agile team. (The second thing is an [Atlassian](https://www.atlassian.com/) license)

We learn how to *become* agile, rather than *what* agile is, and what we end up with is some mixture called water-scrum-fall.

# Define agile

> able to move quickly and easily.
> "Ruth was as agile as a monkey"
> **synonyms:** nimble, lithe, supple, limber, acrobatic, fleet-footed, light-footed
> **antonyms:** clumsy, stiff, slow, dull

So the main goal of "going agile" in your company is to be able to move quickly and easily.

If you have already gone agile, and you are not moving quickly, easily you have done something wrong.

Lets forget about how. Forget about scrum masters, sprints, stand-up meetings and yellow post-it notes on the white board. All of that are merely suggestions on how to potentially become agile. If you blindly implement this it becomes a ritual. Yet another process. Lets start with the [agile manifesto](http://agilemanifesto.org/):

* Individuals and interactions over processes and tools
* Working software over comprehensive documentation
* Customer collaboration over contract negotiation
* Responding to change over following a plan

Or, let me put this in my own words:

* Individuals and interactions over stand-up meetings, sprints and Jira
* Working software over signed-off BRD
* You have to involve business !
* It's OK to change your mind. It's OK to do it wrong initially.

Most of the time we are just a waterfall team in agile clothing.

# Including your business unit

Usually the business units hear about this thing called agile that will get them their solutions quicker. Of course they want that! Who would not?

They do not really want to know anything more, just deliver quicker please. Use agile!

You have to (really have to!) involve business if you want to become truly agile.

In fact, I would argue that breaking down the line between business and IT will do more for your agility than any sprint or stand-up meeting.

# It's not about speed
![speed](images/Monkey_methodology/Waterfall_vs_agile.png)

Over time, there might be no difference in the speed of the delivery if you compare waterfall to agile.

Who knows? (It will be very hard to compare.) But let's say for example that you know exactly what you want. You:

* plan for a month
* do some analysis and specs for another month
* create some architecture documents for another month
* implement the solution in 2 more months
* do a month of testing
* and do a big bang, flawless implementation into your production environment over some weekend.

6 months later and you have your solution in prod, working like a dream.

Doing this the agile way might also take 6 months, maybe more, maybe less. You are just implementing this, feature by feature, into production until all is done.

Agile is not about speed!! It's about the fact that we rarely know what we want. (Let alone what our customers want) It's about the ability to change, quickly.

So going agile allows us to move the end goal around. We do not have to know where we will end up. Let your customers guide you.

So when the client (or business) says "Hey, what about this ?" or "Can we please add feature x ?"  or  "Why did you not think of y ?"  - in a waterfall project, if all went well, you say "No problem, I did think of that, let me quickly enable that." (yea right!) where in agile you say "No problem let's quickly add it."

# Project vs. Product
That's why it's also better to think of a product rather than a project. A project usually has a start time, end time and a set of features. A product has versions and features and evolve over time. A Product might never be done. That's OK. We are not building a house :)
![product](images/Monkey_methodology/Waterfall_vs_agile2.jpg)

# Adaptive vs. Predictive

Another nice way to think about it is Adaptive vs. Predictive. With waterfall you have to predict into the future what needs to happen. You have to get this right, else you waste time (and we all know time is money).

That is why you plan. That is why you architect to cater for all sorts of possible changes. There are many books with all sorts of patterns to help you implement a system that can do more than what the client wants to try and cater for future changes. You have to get it spot on though, and that is close to impossible.

Don't get me wrong, I am not saying that implementing architectural patterns is wrong. It's just that sometimes we over engineer a system because we need to predict the future. We should rather become more adaptive to change. (because change is the only constant).

# Agile principles

Lets re-look some of the agile principles, and make sure that we adhere to this rather than blindly follow some rituals... (Yes - you can be agile without stand-up meetings, sprints and post-it notes)

> *"Business people and developers must work*
> *together daily throughout the project."*

This is where the stand up meetings come from. The goal behind the stand-up meeting is to force business and developers to interact. You can also achieve this in another way, eg. make the project teams (business and developers) sit together.

> *"Build projects around motivated individuals.*
> *Give them the environment and support they need,*
> *and trust them to get the job done."*

and

> *"The best architectures, requirements, and designs*
> *emerge from self-organizing teams"*

Very important to give them the environment and freedom to get the job done. (see [Guerrilla programming]({{< relref "Guerrilla_programming.md" >}}))

> *"Continuous attention to technical excellence*
> *and good design enhances agility."*

and

> *"Simplicity--the art of maximizing the amount*
> *of work not done--is essential."*

Do not do big up-front architecture. Evolutionary architecture and clean design principles is the way to go.

 > *"Working software is the primary measure of progress."*

 Yip - not the number of Jira numbers closed!

 > *"Welcome changing requirements, even late in development. Agile processes harness change for the customer's competitive advantage."*

 We do not complain that business changed their mind again, in fact we encourage it. Change is OK!

> *"Our highest priority is to satisfy the customer*
> *through early and continuous delivery*
> *of valuable software."*

We need to get working software in Production, no big bang implementation. (Remember Production does not necessarily mean Live).

See [http://agilemanifesto.org/](http://agilemanifesto.org/) for more.

#  Conclusion

Don't get caught up in the rituals.

Extreme Programming, SCRUM, DSDM, Adaptive Software Development, Crystal, Feature-Driven Development, Pragmatic Programming...

These are all great methodologies that all got together to create the agile methodology. It does not really matter what methodology you follow, stick to the agile principles. Be pragmatic. So do not let your rituals, processes and tools force you in a direction, let your agility use processes and tools to help implement the agile principles. If it does not work for you let it go. Measure yourself against the delivery of working software. Continually improve.

Do not try and force agile onto a team, rather make sure that the natural operation of your team is agile. Enable them to become agile.

**Agile like a monkey.**
