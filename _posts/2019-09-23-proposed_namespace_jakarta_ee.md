---
title: Proposed namespace for Jakarta EE
tags: [java, Jakarta EE]
image: "/images/jakartaee.jpg"
bigimg: "/images/Proposed_namespace_Jakarta/banner.jpg"
---

**Disclaimer:** This is my personal opinion and does not represent the view of my employer.

By now everyone knows that we need to rename all `javax` package names to something else due to the move from Oracle to the Eclipse Foundation.

(For reference see Appendix A)

At the moment the discussion is basically, should we do this rename all at once, or phased as we update APIs (I believe we should do it all now and get it over with). Currently most people assume 
we will rename all `javax` to `jakarta`. 

Another discussion that is going on is the relationship between MicroProfile and Jakarata EE. I believe these 2 groups should stay separate, but work together. However how that will work is still under discussion.

# Decouple the brand from the specification.

At the moment all specifications under MicroProfile use the namespace (or package name) `org.eclipse.microprofile` and as mentioned the proposal for Jakarta EE is `jakarta`.
Similar the `groupId` and `artifactId` of both of these contains the "brand" name (Jakarta or MicroProfile).

![curent](/images/Proposed_namespace_Jakarta/current.png)

Although this makes sense and most people would argue that the brand should be visible in both these, this couples the spec to the brand or grouping.

My proposal is to remove the brand from the namespace and maven artifacts. This means we lose some branding element, but gain flexibility. 
By removing the grouping/branding from the specification, the only thing that tie that specification to a certain umbrella project is the workgroup that work on these 
specifications. (The group of people working on this specification)

As an example, rather than renaming from `javax` to `jakarta`, we rename to something more neutral, like ~~org.eclipse.enterprise~~ `something.brandless.and.neutral`. (Not sure wat this should be - see update below)

Because we can make breaking changes in MicroProfile, we also remove the brand from MicroProfile APIs, and also rename the APIs to ~~org.eclipse.enterprise~~ `something.brandless.and.neutral`.

![curent](/images/Proposed_namespace_Jakarta/proposed.png)

# MicroProfile and Jakarta EE relationship.

At the moment, MicroProfile depends on Jakarta EE (but not the other way around). I would suggest to keep it that way, so only have that relationship go one way.

MicroProfile is a nice place for innovation and experimentation, but not (necessary) for backward compatibility. This allows MicroProfile to move quickly when 
creating new specifications.

However, at some point, new APIs do mature and become somewhat feature complete. When no new major features are added, the API becomes fairly stable (in terms of features). 
At this point it might be worth it to see if the work should move to a Jakarta workgroup, and from then onward support backward compatibility. 
MicroProfile umbrella would still depend on it, however in the same way that it depends on the other APIs it pulls from Jakarta EE.

Because of the above suggested naming, there would be **no** change in the code and users can continue as normal, with the only difference that Jakarta EE has a new API 
and that specification is now being maintained under another workgroup and another Eclipse process. 

## Config example.

If we use config as an example, the API has matured under MicroProfile, and is (potentially) at a point where it can move the workgroup to Jakarta. Having another standard under Jakarta or JSR
as it is now, might not be a good idea. A Standard is only a standard if there is only one. In my opinion [JSR382](https://github.com/eclipse/ConfigJSR) should retire in favor of the MicroProfile Config API that moves to Jakarta.

# Future Groupings.

Once we have a mechanism to move APIs between brands or groupings without affecting the code and the clients, we can consider other groupings and this might be a way to "retire" some APIs from Jakarta.

Example, if we create a new grouping under eclipse, let's call it ClassicProfile, we can, as with MicroProfile, let ClassicProfile depend on Jakarta, but not the other way around. We can then move older APIs that we do 
not want in Jakarta anymore to this profile. This means that clients that use these can still get them without code changes. This allows us to put Jakarta EE on a diet to get it thinner.

![curent](/images/Proposed_namespace_Jakarta/legacy.png)


# Final thoughts

As with most Architectural decisions, there is a trade-off. In this case, branding vs portability. However I believe it’s worth it. 
It is also important to act now, we have this one chance to rename the current javax namespace, so it’s now or never.

# Update
After some discussions: `org.eclipse.*` is a bad idea as this ties us to the eclipse foundation. Not sure what this neutral name should be though.

# Appendix A  

* [The original announcement](https://eclipse-foundation.blog/2019/05/03/jakarta-ee-java-trademarks/) and this [twitter thread](https://twitter.com/mmilinkov/status/1125213654775889921?s=19)
* [Mark Little from Red Hat](https://markclittle.blogspot.com/2019/05/to-enterprise-java-and-beyond-personal.html?m=1)
* [Ian Robinson, Kevin Sutter from IBM](https://developer.ibm.com/announcements/jakarta-ee-has-landed/?hss_channel=tw-939323243076259842)
* [Steve Millidge from Payara](https://blog.payara.fish/jakarta-ee-8-and-beyond?utm_content=90822134&utm_medium=social&utm_source=twitter&hss_channel=tw-2599580401)
* [David Blevins from Tomitribe](https://www.tomitribe.com/blog/jakarta-ee-a-new-hope/)
* [Ivar Grimstad](https://www.agilejava.eu/2019/05/05/jakarta-going-forward/)
* [Mark Strubert](https://struberg.wordpress.com/2019/05/06/the-way-forward-for-jakartaee-packages/)
