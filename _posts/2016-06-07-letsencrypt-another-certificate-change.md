---
title: Let's Encrypt renewal not fun at all
tags:
  - letsencrypt
  - ssl certificate
  - renew
date: 2016-06-07
---
I like old times, when certificate was valid for one year period. But with letsencrypt certificate issued for 90 days period.  
More above most people renew certificate every 60 days with letsencrypt.  
I use [Certificate Patrol](http://patrol.psyced.org/) addon with Firefox and every time certificate changes I really see it.   
Also I can personally remember certificate SHA1 or SHA256 (partly) fingerprints for websites I visiting often.  
<!--more-->And I think this is a good practice. But its harder to detect certificate change by bad guys when certificate change itself every 60 days by site owner. And totally not possible to remember fingerprint personally.  
Thats why I still use old StartSSL when I can use it. Automatic ssl certicates update is evil, when you can't remember cerificate fingerprint for you own website.  
**What can you do?**    
Basically you can use [same CSR with letsencrypt](https://community.letsencrypt.org/t/using-already-issued-csr/1772) with `--csr` option for one year period for example.  
Post [you may interested](https://security.stackexchange.com/questions/27810/should-i-change-the-private-key-when-renewing-a-certificate) in too.

> This post will be also like collection of dead certificates fingerprints in future.
