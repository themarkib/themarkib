---
title: 'HTB - Bizness'
author: themarkib
date: 2024-01-23 20:55:00 +0800
categories: [Hack The Box, CTF]
tags: [HTB,hackthebox,CTF,bugbounty]
---

Hello everyone,It’s me Bikram Kharal here to write a about a easy hackthebox machine called as Bizness.Lets’ start :

**Initial Enumeration**

First of all i did a simple nmap scan to enumerate all the ports in the box.Here few ports like 22,80,443 seems interesting.

![](https://miro.medium.com/v2/resize:fit:700/1*LP8zRGiBYRsV4bFgu9fnzQ.png)

I added  [https://bizness.htb/](https://bizness.htb/)  to  _/etc/hosts_  in my linux machine.

**Enumerating Bizness Website**

Visiting the website we found it as a static site.

![](https://miro.medium.com/v2/resize:fit:700/1*ZAnefeeh-xEAn-v3NWjlLQ.png)

After directory bruteforcing we got a path called control.

![](https://miro.medium.com/v2/resize:fit:700/1*Q4bEltjK6OHWozNEdDmZPg.png)

Visiting a /control path showed nothing.I again did recursive fuzzing with control path and got a few paths like:

![](https://miro.medium.com/v2/resize:fit:700/1*PAKDW7vsx7mbGBStjW7ejg.png)

I tried to visit /control/login and following login page appeared.

![](https://miro.medium.com/v2/resize:fit:700/1*v3sbbW-1zukkgnCTvYsn6w.png)

After researching I found that the page was of Apache Offbiz framework.I tried searching for vulnerabilities of Apache Offbiz framework and got the recent CVE-2023–51467. After few time i found a exploit in the github.

[](https://github.com/jakabakos/Apache-OFBiz-Authentication-Bypass?source=post_page-----4be1c40587f1---------------------------------------)

## GitHub - jakabakos/Apache-OFBiz-Authentication-Bypass: This repo is a PoC with to exploit…

### This repo is a PoC with to exploit CVE-2023-51467 and CVE-2023-49070 preauth RCE vulnerabilities found in Apache OFBiz…

github.com

I tried a netcat command to get a user shell with the above exploit.

![](https://miro.medium.com/v2/resize:fit:700/1*AzOqEyzhHOBxXyhXTuoBeA.png)

And we got our shell in the nc.

![](https://miro.medium.com/v2/resize:fit:700/1*EK2oUNY7wPv5nfTOdXNypg.png)

Going to /home/ofbiz we got user.txt flag.

![](https://miro.medium.com/v2/resize:fit:372/1*UxMyL5NiXmNoOEUqF_9HUg.png)

This much for today. I will update the Privilege Escalation to root in few days.

Thank you everyone for reading this.