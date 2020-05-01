---
layout: post
comments: true
title:  "Review: Pi-hole on Windows 10"
date:   2020-04-24 14:49:00 +0200
tags: [Pi-hole, Docker, Ad-blocking, hacker project, Windows 10,]
image: /assets/img/review-living-with-pi-hole-on-windows-10.png
---
![Pi-hole shaped heart]({{site.baseurl}}/assets/img/review-living-with-pi-hole-on-windows-10.png)

I’ve been running Pi-hole on my Windows 10 PC for over four months now. When I initially installed Pi-hole on my PC [I wrote a short set-up guide]({{site.baseurl}}/2020/01/25/installing-pi-hole-on-windows-10.html). I did this purely so I could remember the steps I took to get it working on Windows 10, but was completely blown away by the reception. This blog which normally gets around twenty visits a week started to get thousands of visits, all to the post about installing Pi-hole on Windows 10. 

It’s clear that there’s a lot of interest in this topic, so I thought it was a good time to share some of my day-to-day experiences of running Pi-hole on Windows 10 and review whether it’s actually worth it.  I’ll explore some of the areas that running Pi-hole on Windows 10 excels and then go into some detail of some of the problems I encountered. It’s also worth mentioning that much of this is also applicable if you’re running Pi-hole on a Raspberry Pi, Linux PC or Mac.


### An all-round better Ad blocker

There’s not really much to say about Pi-hole’s ability to block ads other than it’s utterly excellent. It just works. Web browsing with Pi-hole is a faster, less cluttered, cleaner experience. I won’t be going back.

Having said that, Ad blocking is arguably a commoditised feature at this point and this wasn’t my main reason for switching to Pi-hole. For me the biggest appeal of Pi-hole was it’s ability to bypass annoying ad blocker detection. I’d been using a browser based ad blocker, AdblockPlus for years. It was reasonably effective and did a good job of propagating across my different devices because of Google Chrome’s useful (or creepy) sync feature which ports your favourites, history and ad-ons to wherever you’re signed in. The biggest drawback however was the increase in annoying “Ad blocker detected” messages displayed on websites. While Adblock plus makes it pretty straightforward to disable the ad blocking for specific websites, it quickly becomes frustrating. Pi-hole makes this a thing of the past.

In my experience, Pi-hole is 100% effective at bypassing Ad blocker detection.  I’ve seen absolutely no “Ad blocker detected” pop-ups when browsing the since switching to Pi-hole. 

Below is an example of the same page when viewed using Pi-hole (left) and AdblockerPlus (right):

![The same page viewed using AdblockerPlus and Pi-hole]({{site.baseurl}}/assets/img/pi-hole-vs-adblock-plus.png)
The other main advantage of Pi-hole over other ad blockers is it’s ability to work on all devices on your network. While Android and iOS now support ad blocking functionality through apps and ad-ons, in my experience it’s always been a clunky experience that doesn’t work everywhere. Also, with this approach you’re effectively trusting a 3rd party commercial app with all of your browsing data. 

For network-wide ad blocking Pi-hole wins here hands-down as it requires no extra apps, plugins or software. It works on every single device on your network, and you own and manage your own data through the Pi-hole Admin console. This is the perfect segway to talk about what you can do with that data…


### Use Pi-hole to identify privacy concerns

There was one unexpected benefit of using Pi-hole, it helped me identify devices and apps on my network which were invading my privacy. The Pi-hole admin dashboard provides detailed statistics of the traffic flowing through your network which makes it easy to see what is calling home on your network. If this seems like a little too much for you, you can also disable this functionality and tell Pi-hole not to do any logging.

![Pi-hole Admin console traffic statistics]({{site.baseurl}}/assets/img/pi-hole-detailed-traffic-statistics.png)

In my case the Pi-hole admin dashboard made it clear to see that our Samsung TV was communicating almost non-stop with Samsung owned domains all day and night. This led me to investigate and it turns out Smart TVs are notorious for collecting data about what you watch with a technology called [automatic content recognition, or ACR](https://www.consumerreports.org/privacy/how-to-turn-off-smart-tv-snooping-features/){:target="_blank"}
. I could find no obvious way of disabling this, and the TV itself didn’t seem to include any privacy settings, so I took the nuclear option of simply blocking its network access. We don’t use the in-built apps to watch tv, so this caused no inconvenience at all.

If you’re interested in learning more about how Smart TVs are increasingly spying on their users, Cheddar has produced a really informative video on the subject:

<iframe width="560" height="315" src="https://www.youtube.com/embed/NWwawTnT7LM" frameborder="0" allow="accelerometer; autoplay; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>


### Performance: Pi-hole as a DNS server

How does Pi-hole perform as a DNS server? It is effectively replacing your DNS server after all. Although could run a ton of benchmarks (and I may actually get round to this if there’s enough interest), I think real world, subjective experience may actually be a better judge in this case.

In my experience using the internet has honestly felt a little faster. I haven’t noticed any awkward DNS resolution hangs when loading websites. I’m talking about the kind of delay where you enter a URL, nothing happens for five seconds and then the page loads instantly. This can happen when a DNS server is slow to respond, or cannot be reached.

Overall I’ve been shocked at how well Pi-hole has functioned as a DNS server. In fact when I first set it up, I was skeptical that it was actually working because it was so fast. This even more surprising given that I run Pi-hole on a PC connected to my network via WiFi.  If you’re a professional, unlike me you’ll run this on a wired connection!


### Performance: host PC  performance

What about Windows 10 performance, does running Pi-hole on docker affect my PC’s performance? In my experience, the impact on performance is very modest. My machine is a relatively [low end 2017 Intel NUC](https://ark.intel.com/content/www/us/en/ark/products/95067/intel-nuc-kit-nuc7i5bnh.html){:target="_blank"} with a dual core i5 and a paltry 8GB of ram. Running docker and Pi-hole it still runs smoothly and is completely useable for everything I’d normally use it for such as web browsing, designing with Figma, running a Plex server and playing a few games on Steam. The only impact I have noticed, is that running Docker at startup causes the machine to be a bit less responsive for the first few minutes after boot. This is hardly surprising however as Docker is effectively booting a second OS using Hyper-V. I suspect if you have a more powerful machine with more memory and more cores you won’t even notice this.


### Maintaining and updating Pi-hole

One of my concerns going into this process was Pi-hole on Docker would be complicated to maintain and update. I think this is partially true as there is no way of automatically updating to new versions. This biggest challenge here was working out how it is done as the process of updating to new versions of Pi-hole on docker doesn’t seem to be widely documented. However if you follow these steps documented in [my guide to setting up Pi-hole on Windows 10]({{site.baseurl}}/2020/01/25/installing-pi-hole-on-windows-10.html), it’s not too complicated or time-consuming. It’s also worth pointing out that although Pi-hole is actively maintained, there haven’t been a deluge updates, so you won’t need to do this too frequently.
Link How to update Pi-hole on Docker


### Reliability: what can go wrong?

Overall I found Pi-hole running on Windows 10 to be incredibly reliable. It worked for weeks at a time without needing any attention, however there are a few things to be aware of.


#### Windows 10 Updates
Windows 10 is infamous for its updates. Whether you think this is a good, or a bad thing, the reality is that any Windows 10 PC will restart over night on a relatively frequent basis. In this case, I’d get up in the morning and find my internet wasn’t working.

It is possible to get round this problem however as Microsoft supports a [method for automatically logging on to Windows 10](https://support.microsoft.com/en-us/help/324737/how-to-turn-on-automatic-logon-in-windows){:target="_blank"}. The obvious trade-off with this approach is your PC is no longer secure. Anyone can effectively access your PC by simply turning it off and on again. If this wasn’t bad enough, your password is stored in the Windows registry as plain text. I haven’t enabled this and have opted to put up with the minor inconvenience of occasionally signing into your PC again. I recommend using Windows Remote Desktop to do this. This lets me sign in from whichever device I’m using at the time.

#### Google Shopping and Adverts on Google

This may sound kind of obvious, but Google Shopping and any paid for link on Google will no longer work. This is also the case for services such as Google Analytics, or other B2B analytics services. Although this is exactly what I expected, I often found it surprising as I tend to forget that Pi-hole is running in the background. This can be kind of frustrating. but I have a workaround. Using a VPN service. I use a VPN ([Express VPN](https://www.expressvpn.com/){:target="_blank"}) quite frequently as I’m a Brit living in Denmark and it’s really convenient to be able to visit UK only websites.

![A site blocked by Pi-hole]({{site.baseurl}}/assets/img/pi-hole-blocked-site.png)


#### Using a VPN on your host PC

As I mentioned previously, I use a VPN. One thing I noticed was that if I connected to the VPN on my Windows 10 machine (the same machine which is running Pi-hole) the rest of the devices in my house partially lose internet connectivity. This is because the VPN client makes the Pi-hole DNS server unreachable to other devices on the local network. I found this a little surprising as I’d specified a backup DNS server ([Cloudflare’s 1111 service](https://1.1.1.1/dns/){:target="_blank"}) on my router, and in the case my Pi-hole DNS server is not responding, it should switch to the secondary server. I didn’t investigate this too thoroughly and found the easiest way to get round this is to stop simply stop using the VPN directly on your PC.


### What doesn’t work right now?

There are two areas where I’ve struggled to get Pi-hole on Docker to work the way I hoped it would.

The first is blocking Youtube ads. Before installing Pi-hole I’d heard rumours that it could be used to block those annoying mid-video ads in Youtube, however this doesn’t seem to be the case. Pi-hole will block the banner ads which sometimes appear in embedded ads, but is currently unable to block video ads on Youtube. If I am able to find a way of blocking these, I’ll write about it.

The second thing I’ve not managed to do with Pi-hole on docker is maintain customised whitelists or blacklists in the Pi-hole docker configuration. This is useful if there are certain websites you wish to allow. For example, I don’t mind certain adverts such as listings on Google Shopping when I’m searching for a product to purchase. Even though you can whitelist certain domains with Pi-hole, this is not advisable if you are running Pi-hole on docker as each time you update to a new version, your whitelist configuration is lost. If anyone has successfully configured whitelists in via the docker command, I’d love to hear how!


### Conclusions, is it worth running Pi-hole on Docker?

My experience over the past 4 months has completely reinforced my belief that Pi-hole is hands-down, the best ad blocker available. Its ad blocking is unrivalled, it speeds up your web browsing experience across all of your devices and doesn’t require you to install any additional software on those devices.

But, what about running Pi-hole on Docker on a Windows 10 PC? In reflection, I’d definitely recommend turning any spare PC on your network into a Pi-hole server. It makes the internet a slightly nicer place and isn’t too much of a hassle to maintain even if it does have a few niggles. It’s also a great way of evaluating whether Pi-hole is right for you (and everyone else in your household) before purchasing a Raspberry Pi. 

If it works out, then I’d recommend purchasing a Raspberry Pi to run it on as you’ll have a dedicated device running your Pi-hole server, easier updates and the ability to maintain custom whitelists and blacklists. My next project will be to set up Pi-hole on a proper [Raspberry Pi 4](https://www.raspberrypi.org/products/raspberry-pi-4-model-b/){:target="_blank"} connected via ethernet and see if the experience is any better.

If you haven't already tried running Pi-hole on Windows 10, check out my orignal post [Install Pi-hole on Windows 10 and live ad-free forever]({{site.baseurl}}/2020/01/25/installing-pi-hole-on-windows-10.html).