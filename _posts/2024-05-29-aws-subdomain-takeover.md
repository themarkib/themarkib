---
title: 'Subdomain takeover via AWS s3 bucket'
author: themarkib
date: 2024-05-29 20:55:00 +0800
categories: [Subdomain Takeover, Bug Bounty]
tags: [aws,subdomain,bugbounty]
---

Hello guys,

Today we are going to talk about the Subdomain takeover vulnerability which can be easily identified and exploited by attackers causing maximum impact to the organization.

Subdomain takeover is a common vulnerability that allows an attacker to gain control over a subdomain of a target domain and redirect users intended for an organization’s domain to a website that performs malicious activities, such as phishing campaigns, stealing user cookies, etc.

First of all enumerate all the subdomains using subfinder and run httpx to it.

```
subfinder -dL domain.txt -all | httpx | tee livedomains.txt
```

Now we need to go through a tool called  [Subzy](https://github.com/PentestPad/subzy).It works based on matching response fingerprints from  [can-i-take-over-xyz](https://github.com/EdOverflow/can-i-take-over-xyz/blob/master/README.md).

```
subzy run --targets livedomains.txt
```

If we find the domain vulnerable to AWS s3 bucket takeover. When visiting we get responses like  `NoSuchBucket.`

![](https://raw.githubusercontent.com/themarkib/themarkib/refs/heads/main/img/aws-subdomain-takeover/404.png)

I found that many people are unable to takeover the subdomain though it shown  `NoSuchBucket`  cause they don’t know the region of the AWS s3 bucket.

To find an AWS s3 bucket region, we can simply run a  `dig`  command on our machine.

![](https://miro.medium.com/v2/resize:fit:700/1*teRHytihmafyLSlP92zw2g.png)



After running  `dig`  the command, we can see a few numbers of IPs in the ANSWER SECTION.

Now we need to send a curl request to ipinfo.io to know the AWS s3 bucket region.

![](https://miro.medium.com/v2/resize:fit:623/1*Am7B_Z_i_-AAX0AbU_V7hg.png)

Here we got to know that AWS region is eu-west-1. Hence we can create the above bucket in eu-west-1 region and the bucket will be created successfully.

`Note: During my research i found that if there is IP of cloudfront we can't determine the aws s3 region as the IP belongs to cloudfront.`

To create a AWS bucket you can follow the below steps:

1. Go to the s3 section on AWS and change the region where you want to create the bucket.
   ![](https://raw.githubusercontent.com/themarkib/themarkib/refs/heads/main/img/aws-subdomain-takeover/aws1.png)
2. Now click on the Create Bucket button on the right side.
3. Add the bucket name and turn on public access.
   ![](https://miro.medium.com/v2/resize:fit:700/1*ArZYzdkuVnmcRuAJQpx3zA.png)
4. Now clicking on the create bucket will be succeeded. And the website will start showing below response/error.
   ![](https://miro.medium.com/v2/resize:fit:700/1*wx024AQIl8xRDxQntcmuVw.png)
5. To host our own content or make redirection we need to go to the Properties of the bucket and enable  `Static Web Hosting`
   ![](https://miro.medium.com/v2/resize:fit:700/1*0Yd7VLcokZ_Z4h36IeOFKQ.png)

Successfully we have taken over the AWS s3 bucket.

Thank you all for reading.