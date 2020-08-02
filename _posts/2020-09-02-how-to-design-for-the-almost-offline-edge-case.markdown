---
layout: post
comments: true
title:  "How to design for the almost offline edge case"
date:   2020-08-02 14:02:00 +0200
tags: [Interaction design,Usability,Edge case design, Visual design]
image: /assets/img/almost-offline-edge-case-design.png
---
![Almost offline edge case design]({{site.baseurl}}/assets/img/almost-offline-edge-case-design.png)

Here’s a lesson on designing for edge cases that I learned while travelling around Greece this summer. 

We were lucky enough to be able to hike in the northern mountainous region of Zagoria and then spend some time on the island of the Milos where we mostly ate too much and sat on the beach. Either way, this post is not about my holiday, but about a usability edge case that it exposed me to…

Most of the islands and remote areas in Greece have very patchy reception. For the most part, seeing ‘3G’ on your phone essentially means no data, and ‘4G’ means slow, unreliable data. Eventually, you may be able to load a light web page, but this is mostly going to be a torturous experience. 

Luckily before we travelled, I downloaded everything I would need. Mostly audiobooks, maps podcasts and music. Accessing content with limited internet connectivity has clearly been considered by most app developers. Or so I thought…

In reality, it turns out the way many app developers are approaching cached and offline content is broken.

For example, Google maps when opened in an area with crappy internet connectivity does not display the cached (or manually downloaded) map content, but instead displays a super, low-resolution version of the map. I assume this is because it is attempting to download a full resolution, up to date version of the content. While it’s good to show up to date information such as traffic, in a case where I needed to quickly glance at a map waiting for Google Maps to repeatedly attempt to access a map from Google’s servers which already exists on my device is the absolute worse experience.

In many instances, the only way I could get Google Maps to work was by switching to Flight Mode in order to force the app to use downloaded maps.

![Where am I?({{site.baseurl}}/assets/img/where-am-i.png)]({{site.baseurl}}/assets/img/where-am-i.png)

This isn’t an isolated instance. For example, Audible exhibits the same behaviour. Before playing a downloaded audiobook the app first attempts to connect to Audible - possibly to check if there’s an updated play location. If the app can’t connect to Audible it will either show an error message stating the download content is corrupt (a bug?), or simply just not play the content. Again, the only workaround seems to be to briefly enter Flight Mode and be truly offline

[![Where is my book?]({{site.baseurl}}/assets/img/where-is-my-book.png)]({{site.baseurl}}/assets/img/where-is-my-book.png)

Obviously, not all apps are this bad at handling flaky connections. Spotify for example reliably allows access to and plays downloaded content immediately on an unreliable connection. Netflix also mostly gets this right provided you launch downloaded content from the downloads view in the iOS app. 

I think many of these issues could be avoided if when designing new functionality, we as designers spend a little more time considering the edge cases. For example, offline usage, take the time to really understand all of the situations your users will be in when using your product.

In practice, this means broadening the definition of “offline” to include crappy internet connections. The reason the user downloaded is likely because they knew they would have limited internet access, or possibly wanted to reduce their data usage. In this case, prioritise offline content until a working connection can be established. If the content is downloaded, let the user use it. 
