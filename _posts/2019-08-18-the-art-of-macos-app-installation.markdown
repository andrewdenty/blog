---
layout: post
title:  "The art of Mac app installation"
date:   2019-08-18 19:17:00 +0200
tags: [User Experience, Simplicity, Design, MacOS, Interaction Design,]
---
![Abstract options]({{site.baseurl}}/assets/img/macos-installation-woes.png)


When it comes to app installation, it turns out Mac developers really do **_think different_**.

Have you ever noticed that when you download a MacOS app from the web, the installation experience can differ wildly? How have we not found a consistently reliable, user-friendly way of installing apps?

As far as I can tell, it wasn’t supposed to be this way. 

MacOS was designed to avoid installation headaches by distributing apps as all-in-one ‘bundles’. In theory, apps don’t need to be installed at all as everything the app needs is packaged within the app itself. 

That sounds ingeniously simple, right? Well technically it is, but there’s still the small task of helping users complete the installation by moving the newly downloaded app into the ‘Applications’ folder.

What differs is how app designers handle this task. I’m going to explore four commonly used approaches ranging from theg outright hostile to almost magic…

For each of these examples I’m assuming that the user downloads the app, and then simply opens it.

#### Approach 1 - totally unhelpful:

Some apps refuse to run from the downloads folder.

![Figma MacOS installation]({{site.baseurl}}/assets/img/figma-macos-install-message.png)
In this case the app is aware of what the user needs to do, but instead of helping them with the task, it simply tells the user they’re doing the wrong thing and to try something new.

#### Approach 2 - immediate gratification, future chaos:

Many apps download, and can be run right away from the download folder:

![GitHub MacOS download]({{site.baseurl}}/assets/img/github-macos-download.png)

An app which downloads directly to the ‘Downloads’ folder, and when opened…just opens. 

This achieves the user’s immediate goal of running the app, but it doesn’t help the user keep their apps organised.  A user can end up with multiple versions of the same app installed, and also have problems finding the app in the future as it won’t in the ‘Launchpad’ menu or ‘Applications’ folder. 

While the user can launch the app right away, this method doesn’t help the user keep their computer tidy, or even provide guidance about how to do this. This approach is better suited to more technical computer users (for example the kind of people who would be downloading the GitHub client).

#### Approach 3 - better, but very untidy:
Many apps download in a DMG package which looks a bit like this:

![GitHub MacOS download]({{site.baseurl}}/assets/img/airtame-macos-installer.png)

When you launch the downloaded file, a folder is opened and it encourages you dragging the app into your app folder.

This is really simple, and makes it easy to drag the app right into the app folder and it gives the user confidence they are doing the right thing.  However once it’s installed, the app doesn’t launch immediately, and it leaves a mess on the user’s computer:
- The installation window is still open
- There is a virtual disk image mounted in their system
- The original download is still in the ’Downloads’ folder

#### Approach 4 - almost perfect!

Like approach 2, the download is simply the app. When the app is launched, it immediately asks whether you’d like to move the app to your app folder.

![GitHub MacOS download]({{site.baseurl}}/assets/img/disk-sensei-macos-install.png)
This gives the user freedom to use the app right away with what they were intending to do, but at the same time helps keep their computer organised.

I think this approach can still be improved though. The text is a little ambiguous and leaves inexperienced users with a decision they may not fully understand which isn’t a great feeling. 

Changing the copy to something along the lines of  “The app runs best from the Applications folder” could make it clearer which action to take.

Anyway, that concludes my brief round up of installation approaches. I guess if there’s anything to tie these observations together is that it’s always best to try and anticipate what your users are doing and design experiences which both meet their immediate goals and also help them work in a way which makes it easier for them in the long run.
