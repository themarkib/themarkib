---
title: 'My first Web Cache Poisoning bug'
author: themarkib
date: 2024-01-16 20:55:00 +0800
categories: [Bug, Bug Bounty]
tags: [aws,subdomain,bugbounty]
---
Web cache poisoning is an advanced technique where by an attacker exploits the behavior of a web server and cache so that a  harmful HTTP response is served to other users.

# How does a web cache work?

To understand how web cache poisoning vulnerabilities arise, it is important to have a basic understanding of how web caches work.

If a server had to send a new response to every single HTTP request separately, this would likely overload the server, resulting in latency issues and a poor user experience, especially during busy periods. Caching is primarily a means of reducing such issues.

The cache sits between the server and the user, where it saves (caches) the responses to particular requests, usually for a fixed amount of time.  If another user then sends an equivalent request, the cache simply serves a copy of the cached response directly to the user, without any interaction from the back-end.  This greatly eases the load on the server by reducing the number of duplicate requests it has to handle.

![](https://miro.medium.com/v2/resize:fit:700/1*qPseW70T0yM_F5N-hnJMKQ.png)

Now let’s get back the bug.

As a normal day i was going  through the accquistions of the Sony. I found a static domain and I tried to intercept the request in the burp suite.I tried to explore the whole site but there was nothing new.

![](https://miro.medium.com/v2/resize:fit:700/1*L50NTybLBiwWs9qav2Fz0A.png)

During my BSCP preparation i have checkout the Web Cache Poisoning labs from the portswigger. So i thought of checking web cache poisoning issue on this site.

I already had param miner extension installed on the burpsuite. So i tried to Guess everything using Param Miner extension.After the scanning a burp found the unkeyed header in the request.

![](https://miro.medium.com/v2/resize:fit:700/1*E_0xdlO4amycw_L694Pk5g.png)

Now i tried adding the `X-Forwarded-Host: example.com` , host header in the first request and checked the response.The response was like:

![](https://miro.medium.com/v2/resize:fit:700/1*qqybrUnf6piLvBezkAPPqw.png)

The original host domain was overwritten by the X-Forwarded-Host .

I tried opening the url in the private browser.The example.com was still showing in the response. I was so happy for a moment.

Trying to eslcate it to Stored XSS but i failed as there was AkamaiGhost WAF.

![](https://miro.medium.com/v2/resize:fit:700/1*Ni90HEDSLnmvNDAubODkpw.png)

But i was successful to get HTML injection through this Web Cache Poisoning attack.

I sent the below request:

GET /blog?abcde=234 HTTP/2  
Host: www.test.com  
User-Agent: Mozilla/5.0 (Windows NT 6.4; WOW64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/41.0.2225.0 Safari/537.36  
X-Forwarded-Host: test.com"><h1>themarkib0x0  
Accept-Encoding: gzip, deflate, br  
Content-Length: 2

I have got response like:

![](https://miro.medium.com/v2/resize:fit:700/1*aTC8w_FeyMlQgx9DgBshTQ.png)

I couldn’t escalate more so i reported it to the Sony team in hackerone. After one day it was triaged as high.

Thank  you everyone for reading.

