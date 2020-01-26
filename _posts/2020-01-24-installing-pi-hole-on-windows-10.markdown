---
layout: post
comments: true
title:  "Install Pi-hole on Windows 10 and live ad-free forever"
date:   2020-01-25 15:45:00 +0200
tags: [Pi-hole, Ad-blocking, hacker project, Windows 10,]
image: /assets/img/install-pihole-on-windows-10-using-docker.png
---
![A problematic dialog box]({{site.baseurl}}/assets/img/install-pihole-on-windows-10-using-docker.png)

### What is Pi-hole and why run it on Windows?

[Pi-hole](https://pi-hole.net/){:target="_blank"} is hands-down, the best ad blocker available.  

That's because it acts as a network wide ad-blocker. This means it blocks ads on every single device on your network without any browser extensions clogging up your browser. It even blocks ads inside apps.  

What's more, unlike browser based ad-blockers which just prevent ads from being displayed in your browser, Pi-hole speeds up your browsing experience as it blocks ads from being downloaded in the first place. If that wasn't enough, it even prevents those annoying "*it looks like you're using an ad-blocker*" pop-ups.

All of these benefits did come with one major drawback though. It was originally designed to run on Raspberry Pis. So, unless you had a Raspberry Pi, or a computer running Linux, you were out of luck. However, it's now available for [Docker](https://www.docker.com){:target="_blank"}. This means it can be installed on any device which will run Docker, such as Windows PCs or Macs.

I recently set up Pi-hole on my home network on a Windows PC and so far it's worked flawlessly.  

Anyway, here's a step by step guide to block ads on all of our devices by installing Pi-hole on Windows 10. 


### What you'll need
A Windows 10 PC (Pro or Enterprise edition) which you’re happy to leave on all of the time. Preferably a power efficient, quiet PC. In my case I’m using an Intel NUC.

If you have a Mac, you can also install Pi-hole on MacOS using a similar process to the one described below.

### Step by step instructions

#### Step 1 - Download Docker
 [Download Docker for Windows](https://www.docker.com/products/docker-desktop){:target="_blank"}. As part of this, you will need to create a Docker account.
#### Step 2 - Install Docker
Install Docker, keeping “enable required Windows Features” selected as Docker needs Microsoft Hyper-V which is not enabled by default. 

Avoid clicking the “Use Windows Containers” option. Pi-hole will require Linux containers which is the default. 

![Installing Docker for Windows Desktop]({{site.baseurl}}/assets/img/1-install-docker-windows.png)
#### Step 3 - Configure Docker
When docker is installed the first thing  you will need to do is sign in. Docker lives in the task bar notification area. 

After signing in I recommend tweaking some settings in Docker. You can access docker settings from the docker menu in the task bar tray. I reduced the available Memory to 1GB. Pi-hole doesn’t actually need 2GB, so reducing it to 1GB frees up more memory for your PC. Click ‘Apply and restart’.

![Installing Docker for Windows Desktop]({{site.baseurl}}/assets/img/2-docker-windows-resources-configuration.png)
#### Step 4 - Download Pi-hole
To download the Pi-hole container, open Windows Command Prompt as an administrator and type the following command: ```docker pull pihole/pihole``` 
#### Step 5 - Give your PC a static IP address
Next, let’s ensure our PC has a static IP address. This will ensure other devices can always reach your Pi-hole server without any issues. To do this:
- Navigate to **Start menu** > **Settings** > **Network and internet** > **Network and Sharing Center** > **Change adapter settings** > Right click on your active network connection and click **Properties**. Double click on **Internet Protocol Version 4 (TCP/IPv4)** and select **Use the following IP address**.  
- Set a manual IP address. If you are unsure what to use for the subnet mask or default gateway, type ```ipconfig``` in the command prompt and reuse the same values for the IP address. 
- I recommend increasing the last number of your IP address by 50 or so to make IP address conflicts less likely on your network. For example, if your IP address was 192.168.0.5 try 192.168.0.55.  
- For DNS settings, use the IP address of your machine you have just set and for an alternative use ```1.1.1.1.``` (this is [Cloudflare's DNS service](https://1.1.1.1/dns/){:target="_blank"}).

![Installing Docker for Windows Desktop]({{site.baseurl}}/assets/img/3-windows-change-to-static-ip.png)

#### Step 6 - create a customized Docker command
Now Docker is running and Pi-hole is downloaded we can configure and start it. You will need to customise your script. Here’s a base:


```docker run -d --name pihole -e ServerIP=172.16.154.130 -e WEBPASSWORD=password -e TZ=Europe/Copenhagen -e DNS1=127.0.0.1 -e DNS2=1.1.1.1 -e DNS3=1.0.0.1 -p 80:80 -p 53:53/tcp -p 53:53/udp -p 443:443 --restart=unless-stopped pihole/pihole:latest```

**You will need to replace:** 
- ```ServerIP``` with your IP address
- ```WEBPASSWORD``` with a password of your choosing (you’ll use this to access Pi-hole’s settings)
- ```TZ=``` this is optional. You can specify your timezone in the [TZ format](https://en.wikipedia.org/wiki/List_of_tz_database_time_zones){:target="_blank"}

The base script above will get you up and running, however if you want to customise how Pi-hole works, there are a [number of variables you can set as part of this script](https://github.com/pi-hole/docker-pi-hole#environment-variables){:target="_blank"}.

You'll also find more information and documentation on the [Docker Pi-hole Docker Github page](https://github.com/pi-hole/docker-pi-hole){:target="_blank"}. 

#### Step 7 - run your script and start your Pi-hole server 
Open command prompt as an administrator again and paste in your customised command and press enter. This will create your Pi-hole Docker container and run it.

#### Step 8 - Check Pi-hole is up and running

Go to your web browser and type - [http://127.17.0.1/admin/](http://127.17.0.1/admin/){:target="_blank"} or http://localhost/admin/  you should see the Pi-hole admin console. This means you’re up and running!

![Pi-hole admin interface]({{site.baseurl}}/assets/img/4-pi-hole-admin-console.png)

The Pi-hole admin console lets you configure the advanced settings of Pi-hole, see which domains have been blocked as well as  blacklisting or whitelisting new domains.

#### Step 9 - Configure your router
The final step is to change the DNS server on your router to point to your PC. In my case this is ```172.16.154.130```. This will ensure all of the devices on your network get Pi-hole’s ad blocking magic.
