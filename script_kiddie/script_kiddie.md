# HackTheBox - ScriptKiddie

## Introduction
<p align="justify">
Hello Friend! You have come a long way following me here. So let me show you how i solved this challenge named <b><u>ScriptKiddie</u></b>. You with me ? Good ! Let's do it.
</p>

### 1. Enumeration
When I joined the machine, I got an *ip_address* of **10.10.10.226**. The first thing i tend to do when i have an *ip_address* is to see if a http website is up and running. So i manually visited the site but got nothing.

Then i fired up my `nmap` scan to see all the active ports it can find.

```bash
nmap -sC -sV 10.10.10.226 -oN recon/nmap_initial
```

I often scan top ports and save its response for future references as well as a quick guide for me. Again i fired up another `nmap` scan to look for all open ports.

```bash
nmap -p- 10.10.10.226 -oN recon/nmap_all_ports
```

I let it run in the background. All ports scan is just for the case not to miss anything.

![](2021-05-07-09-13-35.png)

First `nmap` scan returned me with an output saying there's a `ssh` service running on port *22*. And the interesting thing is there is a `http` service running on port *5000*. `Nmap` also said that this is a ***Werkzeug httpd 0.16.1*** server.

I thought as the title suggests, this box may have a well known vulnerability which should be exploited. So i rushed and searched for **Werkzeug** exploit.

```bash
searchsploit Werkzeug
```

![](2021-05-07-09-19-16.png)

Indeed there is an exploit for **Werkzeug** server leading to *RCE*. I quickly copied the exploit.

```bash
searchsploit -m multiple/remote/43905.py
```

I decided not to be a complete script kiddie and i took a sneak peek at the exploit. I quickly found out that the exploit only works if debug mode is enabled. If the debug mode is enabled, then you will have a */console* page.

```bash
curl http://10.10.10.226:5000/console
```

I saw there wasn't any page called console. Maybe the */console* endpoint is ofuscated, so let's have a fuzzing here

```bash
ffuf -u http://10.10.10.226:5000/FUZZ -w /mnt/hack/wordlist/common.txt
```

![](2021-05-07-09-25-57.png)

I didn't find anything so i concluded that the site does not have debug mode enabled and to our dismay, our exploit for quick *RCE* does not work here. :disappointed: