---
layout: post
comments: true
title:  "How to Install Pi-hole on Windows 11 with Docker"
description: "Block ads on every device at home by running Pi-hole on Windows 11. A step-by-step Docker guide, updated for Pi-hole v6 in 2026, plus fixes for common problems."
date:   2022-03-13 10:05:00 +0200
last_modified_at: 2026-10-29 12:00:00 +0100
tags: [Pi-hole, Docker, Ad-blocking, hacker project, Windows 11, Privacy]
image: /assets/img/install-pihole-on-windows-11-using-docker.png
---
![Pi-hole on Windows 11]({{site.baseurl}}/assets/img/install-pihole-on-windows-11-using-docker.png)

[Pi-hole](https://pi-hole.net/){:target="_blank"} is a free ad blocker for your whole home network. It works as your network's DNS server, the service your devices use to look up websites. When a phone, laptop or smart TV tries to load an ad or a tracker, Pi-hole blocks the request before anything is downloaded. Ads disappear on every device, even inside apps, without installing anything on them.

Pi-hole was designed for the Raspberry Pi, but thanks to Docker you can run it on a Windows PC you already own.

Back in 2020 I wrote a step-by-step guide to [installing Pi-hole on Windows 10]({{site.baseurl}}/2020/01/25/installing-pi-hole-on-windows-10.html). I definitely wasn't prepared for the response! The post has had hundreds of thousands of visits, and heaps of helpful comments that helped me refine the instructions. I wrote it as a go-to guide for myself, and somehow it became one for a whole community of tinkerers. This Windows 11 version followed in 2022 when I moved to a new PC, and I keep it up to date as Pi-hole, Docker and Windows change.

> **Updated October 2026 for Pi-hole v6.** Pi-hole v6 changed how passwords and settings work in Docker, so older instructions (including earlier versions of this guide) no longer work. If you can't log in to Pi-hole, [jump to the fix](#cant-log-in).

If you're wondering whether it's worth running Pi-hole on Windows, or whether you should just buy a Raspberry Pi, take a look at my [review of living with Pi-hole on Windows]({{site.baseurl}}/2020/04/24/review-living-with-pi-hole-on-windows-10.html).

**Jump to:** [Step-by-step guide](#step-by-step) · [Updating Pi-hole](#update) · [Moving from an older version of this guide](#upgrade) · [Troubleshooting](#troubleshooting) · [FAQs](#faqs)

### What you'll need {#what-youll-need}
- **A Windows 11 PC that you're happy to leave on all the time.** A small, quiet, power-efficient PC is ideal. In my case I'm using an Intel NUC. Windows 11 Home and Pro both work.
- **At least 8 GB of RAM.** That's Docker Desktop's minimum. Pi-hole itself only needs a fraction of it.
- **Virtualisation turned on in your PC's BIOS.** It's on by default on most PCs, and Docker will tell you if it isn't.
- **Access to your router's settings**, so you can point your network at Pi-hole.

Before you start, run Windows Update so your PC is fully up to date. Then stop your PC from going to sleep. Go to **Settings > System > Power & battery** (just **Power** on some desktop PCs) and set your PC to never sleep when it's plugged in. If your PC sleeps, so does Pi-hole, and the devices on your network won't be able to load websites.

### How to install Pi-hole on Windows 11, step by step {#step-by-step}

#### Step 1: Install WSL {#step-1}
Docker uses the Windows Subsystem for Linux (WSL) to run Linux software, like Pi-hole, on Windows. Right-click the **Start** button, choose **Terminal (Admin)**, and run:

```powershell
wsl --install --no-distribution
```

When it's finished, restart your PC.

#### Step 2: Install Docker Desktop {#step-2}
1. Go to the [Docker Desktop download page](https://www.docker.com/products/docker-desktop/){:target="_blank"} and choose **Download for Windows – AMD64**. This is the right version for almost all PCs, including ones with Intel processors. You don't need a Docker account.
2. Run the installer and stick with the recommended options. If it asks how to install, choose the recommended option for your user account, and keep **WSL 2** as the backend. If the installer asks you to restart or sign out at the end, do that.
3. Open **Docker Desktop** from the Start menu and accept the Docker Subscription Service Agreement. Docker Desktop is free for personal use. You'll also be asked to sign in or create an account. This is optional, so you can skip it.
4. Open Docker Desktop's **Settings** (the cog icon at the top) and, under **General**, turn on **Start Docker Desktop when you sign in to your computer**, then click **Apply**. This is off by default, but it's important: without it, Pi-hole won't come back on after your PC restarts.

#### Step 3: Give your PC a static IP address {#step-3}
Every device on your network needs to be able to find Pi-hole, so your PC needs an IP address that never changes.

1. Open **Terminal** and type `ipconfig`. Under your Wi-Fi or Ethernet adapter (ignore any "vEthernet" entries), make a note of your **IPv4 Address**, **Subnet Mask** and **Default Gateway** (this is your router's address).
2. Open **Settings > Network & internet**. Click **Ethernet**, or if you're on Wi-Fi, click **Wi-Fi** and then your network's properties.
3. Under **Network profile type**, choose **Private network**. This tells Windows it's your trusted home network, so other devices can reach Pi-hole.
4. Next to **IP assignment**, click **Edit**. Choose **Manual** and turn on **IPv4**.
5. Fill in the following:
   - **IP address:** your current IP address with the last number changed to something higher, which is less likely to clash with other devices. For example, if your IP address is `192.168.1.23`, use `192.168.1.200`.
   - **Subnet mask** and **Gateway:** copy these from `ipconfig`. The subnet mask is usually `255.255.255.0`.
   - **Preferred DNS:** the same IP address you've just chosen, so your PC uses Pi-hole too. Leave **DNS over HTTPS** off.
   - **Alternate DNS:** `1.1.1.1` ([Cloudflare's DNS service](https://1.1.1.1/dns/){:target="_blank"}). This keeps your PC online if Pi-hole isn't running.
6. Click **Save**. You only need to configure IPv4.

![Windows 11 Edit network IP settings dialog with a manual IP address and Pi-hole as the preferred DNS server]({{site.baseurl}}/assets/img/5-windows-11-set-static-ip-address.png)

**Tip:** if you're comfortable with your router's settings, you can reserve an IP address for your PC there instead (often called *DHCP reservation* or *address reservation*). Either approach works.

#### Step 4: Create your Pi-hole settings file {#step-4}
Pi-hole's settings live in a small text file called `docker-compose.yml`. Docker reads this file to set up Pi-hole for you, and it makes updating Pi-hole much easier later on.

Open **Terminal** (you don't need admin this time) and run these commands to create a folder for Pi-hole and a new file inside it:

```powershell
mkdir C:\pihole
cd C:\pihole
notepad docker-compose.yml
```

Notepad will ask whether you want to create a new file. Click **Yes**, then copy and paste the following into Notepad:

```yaml
services:
  pihole:
    container_name: pihole
    image: pihole/pihole:latest
    ports:
      # DNS: how your devices talk to Pi-hole
      - "53:53/tcp"
      - "53:53/udp"
      # Pi-hole's web dashboard
      - "80:80/tcp"
      - "443:443/tcp"
    environment:
      # Your timezone
      TZ: 'Europe/London'
      # The password you'll use to log in to the dashboard
      FTLCONF_webserver_api_password: 'choose-a-password'
      # Where Pi-hole sends the requests it doesn't block (Cloudflare)
      FTLCONF_dns_upstreams: '1.1.1.1;1.0.0.1'
      # Lets the other devices on your network use Pi-hole
      FTLCONF_dns_listeningMode: 'ALL'
    volumes:
      # Keeps your settings and blocklists when you update Pi-hole
      - pihole_data:/etc/pihole
    cap_add:
      # Lets Pi-hole keep its clock accurate and run smoothly
      - SYS_TIME
      - SYS_NICE
    restart: unless-stopped

volumes:
  pihole_data:
```

**You will need to change:**
- `choose-a-password`: the password you'll use to log in to Pi-hole. Keep the quotes around it, and avoid using `$` or `'` in your password, as they confuse Docker.
- `Europe/London`: your timezone, from this [list of time zones](https://en.wikipedia.org/wiki/List_of_tz_database_time_zones){:target="_blank"}. For example, `America/New_York` or `Australia/Sydney`.

Leave the spaces at the start of each line exactly as they are, because Docker is fussy about them. Then save the file (**Ctrl + S**) and close Notepad.

You can leave everything else as it is. If you'd prefer a different DNS provider, you can swap the Cloudflare addresses for another one, such as Quad9 (`9.9.9.9;149.112.112.112`). There are also [lots of other settings you can add](https://docs.pi-hole.net/docker/configuration/){:target="_blank"} if you want to customise Pi-hole.

**Good to know:** anything set in this file is locked in Pi-hole's dashboard. To change it later, edit the file and run `docker compose up -d` again.

#### Step 5: Start Pi-hole {#step-5}
Back in Terminal, in the `C:\pihole` folder, run:

```powershell
docker compose up -d
```

Docker will download Pi-hole and start it. The first time, this takes a minute or two. When it's done, you'll see that the `pihole` container has started, and it'll also appear under **Containers** in Docker Desktop.

You'll probably see a Windows Firewall pop-up for **Docker Desktop Backend**. Make sure **Private networks** is ticked and click **Allow access**. If you skip this, the other devices on your network won't be able to reach Pi-hole.

![Windows Defender Firewall pop-up asking to allow Docker Desktop Backend on private networks]({{site.baseurl}}/assets/img/6-windows-11-firewall-dialog-pi-hole.png)

#### Step 6: Log in to the Pi-hole dashboard {#step-6}
Open your web browser and go to [http://localhost/admin](http://localhost/admin){:target="_blank"}. Log in with the password you chose in Step 4. If the page doesn't load straight away, give Pi-hole another minute to finish starting up.

From the dashboard you can see how many ads and trackers have been blocked, manage your blocklists, and allow or block individual websites. To open it from another device on your network, use your PC's IP address instead, for example `http://192.168.1.200/admin`.

#### Step 7: Point your router at Pi-hole {#step-7}
The final step is to tell your router to use Pi-hole as the DNS server for your network. This gives every device on your network Pi-hole's ad blocking magic, with nothing to install.

Log in to your router's settings page. You can usually do this by typing your Default Gateway address from Step 3 into your browser. Find the **DNS server** setting (often under the LAN or DHCP settings) and change it to your PC's IP address, for example `192.168.1.200`.

Every router is different, so if you get stuck, search for your router's model name and "change DNS server". If your router doesn't let you change it, see the [FAQs](#router-dns).

Devices pick up the change when they next reconnect, so restart them or turn their Wi-Fi off and on again.

#### Step 8: Check it's working {#step-8}
Browse a few websites on your phone or laptop. Ads should be gone, and in the Pi-hole dashboard you'll see **Queries Blocked** start to climb.

For a quick test, run this in Terminal, using your PC's IP address:

```powershell
nslookup doubleclick.net 192.168.1.200
```

If Pi-hole is working, the answer will be `0.0.0.0`. That's Pi-hole blocking one of the internet's biggest ad servers.

Congratulations! At this point you should have a Pi-hole protected home. If something isn't working, check the troubleshooting tips below or leave a comment.

### How to update Pi-hole {#update}
A new version of Pi-hole comes out every month or so. You'll see a message at the bottom of the dashboard when an update is available. To update, open Terminal and run:

```powershell
cd C:\pihole
docker compose pull
docker compose up -d
```

Docker downloads the latest version and swaps it in. Your settings, password and blocklists are kept, because they're stored separately from Pi-hole itself. To free up disk space afterwards, run `docker image prune` and type `y` to delete the old version.

Pi-hole's developers recommend updating by hand rather than automatically, as occasionally an update needs extra changes. It's worth glancing at the [release notes](https://github.com/pi-hole/docker-pi-hole/releases){:target="_blank"} before you update.

### Moving from an older version of this guide {#upgrade}
If you set up Pi-hole using an earlier version of this guide (or my Windows 10 guide), you're probably running Pi-hole v5. The `docker run` command in those guides uses settings like `WEBPASSWORD` that Pi-hole v6 no longer understands. It also didn't save your settings outside the container. The easiest way to move to v6 is to back up your settings, then start fresh:

1. Make sure Docker Desktop is up to date. It will prompt you, or you can check under **Settings > Software updates**.
2. In your current Pi-hole dashboard, go to **Settings > Teleporter** and click **Backup** (it's called **Export** if you're already on v6). This downloads a `.tar.gz` file with your blocklists and your allowed and blocked domains.
3. Remove your old Pi-hole. In Docker Desktop, go to **Containers** and delete **pihole**, or run `docker rm -f pihole` in Terminal.
4. Follow [Steps 4 to 6](#step-4) above. Your static IP address and router settings don't need to change.
5. In your new dashboard, go to **Settings > Teleporter**, choose your backup file under **Import**, and click **Import**.

### Troubleshooting {#troubleshooting}

#### I can't log in to Pi-hole {#cant-log-in}
This is usually caused by older set-up instructions. Before Pi-hole v6, you set the password with `WEBPASSWORD`. Pi-hole v6 ignores that setting and creates a random password instead.

To find the random password, open Docker Desktop, click **Containers** and then **pihole**, open the **Logs** tab, and search for "random password". Or run this in Terminal:

```powershell
docker logs pihole | findstr /i password
```

To choose your own password instead, run the following and follow the prompts:

```powershell
docker exec -it pihole pihole setpassword
```

This only works if you haven't set a password in a `docker-compose.yml` file. If you followed this guide, change the password in the file instead and run `docker compose up -d` again. If you're still on the old set-up, I'd recommend [moving to the new one](#upgrade).

#### Docker says a port isn't available
Pi-hole needs port 53 (for DNS) and port 80 (for the dashboard) to be free on your PC.

- **Port 80** is sometimes used by other software, such as a web server. In your `docker-compose.yml` file, change `"80:80/tcp"` to `"8080:80/tcp"`, run `docker compose up -d`, and use `http://localhost:8080/admin` instead. If port 443 is the problem, you can delete the `"443:443/tcp"` line altogether.
- **Port 53** can be taken by Windows itself, for example when **Mobile hotspot** is turned on, or by other DNS software. Turn off Mobile hotspot, or put your PC's IP address in front of the two port 53 lines, like this: `"192.168.1.200:53:53/tcp"` and `"192.168.1.200:53:53/udp"`. Then run `docker compose up -d` again.

#### Pi-hole works on my PC, but not on other devices
This is usually Windows Firewall. Check that your network is set to **Private network** (Step 3). Then search the Start menu for **Allow an app through Windows Firewall**, click **Change settings**, and make sure **Docker Desktop Backend** is ticked under **Private**.

#### Pi-hole stops working after my PC restarts
Make sure **Start Docker Desktop when you sign in to your computer** is turned on (Step 2). Docker Desktop only starts once you've signed in to Windows, so if Windows restarts on its own for an update, Pi-hole stays off until you sign in again.

To avoid this, go to **Settings > Accounts > Sign-in options** and turn on **Use my sign-in info to automatically finish setting up after an update**. Windows will then sign you back in after an update and lock the screen, which should give Docker Desktop the chance to start.

#### Docker says "no configuration file provided"
Make sure you're in the right folder (`cd C:\pihole`) and that the file is called exactly `docker-compose.yml`. Run `dir` to see what's in the folder. If the file has been saved as `docker-compose.yml.txt`, rename it with `ren docker-compose.yml.txt docker-compose.yml`.

#### Docker Desktop won't start
If Docker says WSL needs updating, run `wsl --update` in **Terminal (Admin)** and then reopen Docker Desktop. If it says virtualisation isn't supported, you'll need to turn it on in your PC's BIOS. It's usually called Intel VT-x, AMD-V or SVM.

#### I can still see ads
- **Check the device is using Pi-hole.** Restart it, or turn its Wi-Fi off and on, so it picks up the new DNS settings. If it is using Pi-hole, you'll see requests appear in the **Query Log** as you browse.
- **Check your browser.** Some browsers can use their own secure DNS, which skips Pi-hole. In Chrome or Edge, make sure **Use secure DNS** isn't set to a specific provider. Pi-hole automatically turns off Firefox's automatic secure DNS, unless you've turned it on yourself.
- **Check your router's IPv6 settings.** Some routers also hand out your internet provider's IPv6 DNS servers, which lets devices skip Pi-hole.
- **Some ads can't be blocked by Pi-hole**, such as YouTube video ads (see below).

### FAQs {#faqs}

#### Can Pi-hole block YouTube ads?
Not reliably. YouTube serves its video ads from the same servers as the videos themselves, so Pi-hole can't block one without breaking the other. The same goes for sponsored posts on sites like Facebook and Instagram. A browser ad blocker is still the best option for those.

#### Is Docker Desktop free?
Yes, for personal use, education and non-commercial open source projects. It's also free for small businesses with fewer than 250 employees and less than $10 million in annual revenue. Larger businesses need a [paid subscription](https://www.docker.com/pricing/){:target="_blank"}.

#### What happens if my PC is turned off?
If your router only uses Pi-hole for DNS, the devices on your network won't be able to load websites while your PC is off. Many routers let you add a second DNS server, like `1.1.1.1`, which keeps the internet working when Pi-hole is down. The catch is that devices will sometimes use the backup even when Pi-hole is running, so a few ads will slip through. If you want Pi-hole to be truly always on, a Raspberry Pi is a great option.

#### My router won't let me change the DNS server {#router-dns}
Some routers from internet providers lock this setting. You can still use Pi-hole by setting the DNS server on each device instead. On Windows, it's the **DNS server assignment** setting, next to the IP assignment setting from Step 3. Pi-hole can also act as your network's DHCP server, but unfortunately that doesn't work with Docker Desktop on Windows.

#### Does the PC have to be connected with an Ethernet cable?
No. When I first set this up, my PC was connected over Wi-Fi and it worked really well. Ethernet is a little faster and more reliable though, so use it if you can.

#### Why does the dashboard show all my devices as one client?
Docker Desktop passes network traffic through to Pi-hole on your devices' behalf, so Pi-hole usually sees every request as coming from Docker. Ad blocking still works on every device; you just won't get a breakdown per device. If that matters to you, you'll need to run Pi-hole on a Raspberry Pi or another Linux machine.

#### Can I use a single `docker run` command instead of Docker Compose?
Yes. This PowerShell command sets up Pi-hole with the same settings as the file in Step 4:

```powershell
docker run -d --name pihole -p 53:53/tcp -p 53:53/udp -p 80:80/tcp -p 443:443/tcp -e "TZ=Europe/London" -e "FTLCONF_webserver_api_password=choose-a-password" -e "FTLCONF_dns_upstreams=1.1.1.1;1.0.0.1" -e "FTLCONF_dns_listeningMode=ALL" -v pihole_data:/etc/pihole --cap-add SYS_TIME --cap-add SYS_NICE --restart unless-stopped pihole/pihole:latest
```

I'd still recommend Docker Compose though, as your settings are saved in a file, which makes updating much simpler.

#### Can I do this on a Mac, or on Windows 10?
Yes. On a Mac, install [Docker Desktop for Mac](https://www.docker.com/products/docker-desktop/){:target="_blank"} and use the same `docker-compose.yml` file. On Windows 10, the Docker steps are the same, although Microsoft no longer supports Windows 10, so I'd recommend upgrading if you can.

#### Why use Docker and not a Linux virtual machine?
Docker is easier to set up and maintain. With a virtual machine you need to install, configure and update Linux yourself. With Docker, Pi-hole runs in its own isolated container, and all of your settings live in one file, so you can update or rebuild it in a couple of minutes.

### What's changed in this guide
- **October 2026:** Rewritten for Pi-hole v6, which changed how passwords and settings work. Now uses Docker Compose, covers the latest Docker Desktop and Windows 11, and adds instructions for updating Pi-hole, moving from the old set-up, and troubleshooting.
- **March 2022:** First published, based on my original [Windows 10 guide]({{site.baseurl}}/2020/01/25/installing-pi-hole-on-windows-10.html).

I'll keep updating this guide as Pi-hole, Docker and Windows change. If you run into problems, or spot something that's out of date, let me know in the comments!
