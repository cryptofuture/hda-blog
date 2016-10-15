---
title: Ideal abuse report from site's owner side
tags:
  - abuse report
  - abuse
date: 2016-08-24
updated: 2016-08-22
---
In my [previous post](https://blog.hda.me/2016/07/08/09-hda-down-story-behind-downtime.html) I told about abuse report I missed in spam folder.  
And I can formulate how normal and user-friendly abuse report from my point should look like:
    
- Email subject should be short and clear like `Abuse report to sitename.com` or `Phishing report to sitename.com`
- Message body should be short and as clear as possible
- Try to write message in friendly language, just as you do to your friend. No lawyer language, no legal writing and especially no threats in message

Example:
<pre>
Subject: Phishing report to sitename.com

We discovered phishing link on yours website: https://sitename.com/link35235
Could you delete or change this link, please?

Regards,
Name, Corporation.
</pre><!--more-->

By the way is better to check your security then deal with external resources if you give possibility to embed contents from remote resources or accept POST or another requests from `*` this is your problem. What can you do is:

- check `Access-Control-Allow-Origin` and `Access-Control-Allow-*` headers. Is better when you allow access only from your website or your subdomains, and even if need to include remote resource like github or googleapis for example, you always can list exact resources, then using `*`. 
- enforce `Content-Security-Policy` header, for example thats how github use it:  
`Content-Security-Policy: default-src 'none'; base-uri 'self'; connect-src 'self' uploads.github.com status.github.com api.github.com www.google-analytics.com github-cloud.s3.amazonaws.com wss://live.github.com; font-src assets-cdn.github.com; form-action 'self' github.com gist.github.com; frame-ancestors 'none'; frame-src render.githubusercontent.com; img-src 'self' data: assets-cdn.github.com identicons.github.com www.google-analytics.com collector.githubapp.com *.gravatar.com *.wp.com github-cloud.s3.amazonaws.com *.githubusercontent.com; media-src 'none'; object-src assets-cdn.github.com; script-src assets-cdn.github.com; style-src 'unsafe-inline' assets-cdn.github.com
`
- enforce HSTS(HTTP Strict-Transport-Security) header with preload
- enforce `X-Frame-Options` header, you could also look `Content-Type-Options` and `X-XSS-Protection` headers
- enable Subresource Integrity for js and css

Another more advanced options could be to enable certificate pinning (you can't use it with cheap of free CDN plans), enable DNSSEC (could be possible with cloudflare CDN) and TLSA records for your website. More interesting option will be two-factor authentication, or certificates for clients sign-in (Is very easy to use, idk why this practice is uncommon).
    



