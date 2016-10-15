---
title: HDA Down. Story behind downtime
tags:
  - hda
  - status
  - abuse
date: 2016-07-08
updated: 2016-08-22
---
> hda.me down now. Provider terminated account (without any message or warning), and lost all backups and data. Most likely I'll be looking for another VPS/VDS provider. Since thats not first all data lose with current provider. I can't say now, how long things will take.

I'll give updates on situation.

#### Month and two weeks before downtime
I received abuse message and it got in spam folder. Message was in totally official language and it looked like spam. I even thought that was a joke. 
Message context (copy from cloudflare I received month later, original message was the same, but without extra details):<!--more-->  
<pre>
CloudFlare received a phishing report regarding:

hda.me

Below is the report we received:

Reporter's Name: thomas.m.perreault@irs.gov 
Reporter's Email Address: thomas.m.perreault@irs.gov
Reported URLs:
        https://hda.me/3a1e2
Logs or Evidence of Abuse: Dear Abuse Team,

Your company is hosting a redirect that is being advertised in an 
Internal Revenue Service (IRS) phishing scheme. When victims 
browse to your site, they are redirected to another site that 
is impersonating the IRS.

The site is located at: 
ASN: 13335 
IP: 104.28.3.217 
Defanged URL: hxxps://hda[.]me/3a1e2

We are asking for your assistance removing this fraudulent content as 
quickly as possible and to take the following responses in conjunction 
with your policies.

Secure Your Site 
---------------- 
Your site was likely the victim of a compromise and steps should be 
taken to secure your server and the content that it is providing. 
Please see below for some actions that you may want to implement.

Help Educate Consumers 
---------------------- 
Please see below for instructions if you would like to assist 
in helping to educate consumers about online fraud.

Help Our Investigation 
---------------------- 
As part of our job, we track and analyze phishing information that over 
time may lead to the identification and legal action against these 
phishers. By providing to us any files used in the phish and any relevant 
logs, you would be assisting us in our efforts. 
Please email files, logs or any other relevant information to: submits@ofdp.irs.gov

Additional information regarding this site appears below.

If you have any questions, or require further information, 
please feel free to call me at 1-202-556-2616.

Regards,

Thomas Perreault 
202-552-1226 (Fax) 
Online Fraud Detection and Prevention (OFDP) 
Internal Revenue Service 
United States Department of the Treasury

--------------------------------------------------------------------------

Securing Your Site – Additional Information 
------------------------------------------- 
Your site was likely the victim of a compromise and steps should be 
taken to secure your server and the content that it is providing.

Some actions that you may want to take include: 
- Inspect relevant logs and audit trails. 
- Inspect recently created/modified user accounts and files (including 
hidden files/directories). Phishers generally leave backdoor/shells that 
enable them access back into the server/site if not removed. 
- Ensure files/directories have the appropriate privileges/permissions. 
e.g., web files/directories generally should not be world writable. 
- Ensure web applications have latest security patches and are securely 
configured (including changing default login credentials).

Ongoing monitoring is also strongly suggested, as most phishing sites 
return in a few hours to days if the site is not fully secured. 
For more information see the document from APWG titled: 
What to Do if Your Website Has Been Hacked by Phishers 
http://www.apwg.com/reports/APWG_WTD_HackedWebsite.pdf

Help Educate Consumers – Additional Information 
----------------------------------------------- 
As part of this action, we request that you redirect all traffic going 
to this URL to the following website: 
http://phish-education.apwg.org/r/ 
so that consumers will be educated about phishing if they try to access 
this page. Information about implementing a redirect to this page can be 
found here: 
http://education.apwg.org/r/how_to.html

---------------------------------------------------------------------------------------------------------------------- 
U.S. Department of the Treasury 
Internal Revenue Service 
Online Fraud Detection & Prevention 
IRS PHISHING SITE Summary 
---------------------------------------------------------------------------------------------------------------------- 
Date Entered: 2016-07-06 
Time Entered: 10:44:23 
OFDP Handler: Thomas Perreault <thomas.m.perreault@irs.gov> 
Phone Number: 202-556-2616 
---------------------------------------------------------------------------------------------------------------------- 
URL INFORMATION:

ASN: 13335 
Host IP: 104.28.3.217 
Country: United States 
ISP: CLOUDFLARENET - CloudFlare, Inc., US

Host PTR: 104.28.3.217 
Protocol: https 
Host: hda[.]me 
Path: /3a1e2 
Published URL: hxxps://hda[.]me/3a1e2 
---------------------------------------------------------------------------------------------------------------------- 
----------------------------------------------------------------------------------------------------------------------


<strong>We have provided the name of your hosting provider to the reporter. Additionally, we have forwarded this complaint to your hosting provider.</strong> We have also restricted access to the phishing-related content until it has been removed.

Regards,

CloudFlare Abuse
</pre>

#### After cloudflare report
After I received same abuse message from cloudflare I checked url and this definitely was phishing form located on another website. It was poor made credit card phishing form (and another site was even without https support!) and since I was quite busy, week I received message, I decided to pass this, since url was already blocked by cloudflare. More above I didn't plan to remove redirect, since that was user generated content and I wanted to look into this phishing website in a free time.  
But that was only begining, three days after I lost connection to my website. I thought that was just temporary issue with vps provider, but after I logged in vps control panel on forth day I was highly impressed not to find my vps at all. I checked billing and vps was terminated. And it was pretty obvious that there is a confidence between abuse and vps termination. But after contacted support they told me that maybe I terminated vps myself. And after like week talking with support they didn't confess that this was because of abuse, they didn't even watched (since url was already blocked by cloudflare), and didn't found backups of my vps ether!  

I had like 2 month old local backups, and it was highly troublesome to me to make local backups frequently (and I was dawn busy that month), so I stored backups on vps itself (I know that was stupid, but I had reasons and no time to deal with that). VPS provider was already quite bad, I already lose vps with them before. Someone hacked and wiped all data they had, and that was because provider decided to experimant with CloudLinux, and some security update didn't apply without reboot, and all clients data was wiped. More above after some time they removed friendly backup (friendly becouse that was vzdump abd you could fully restore vps state from it) option from panel, becouse users used their api too frequently, instead limiting api. And there was some downtimes time after time, thats why I didn't want to mess with things much and just waited before paid period will end to move to another provider anyway.

After all [urlhda](https://github.com/cryptofuture/urlhda) and [HDA URL](https://hda.me/) is open-source project and I pay for things from my funds. But live play own game and that was not calm moving. Btw, vps provider made refund only on internal balance I can use only with their services (hate this practice), and don't want use their services even for free now. And I can't do anything with them, since 6 month since payment already passed. What I really don't like is a lie, from provider side. They didn't confess that was abuse, ether didn't share any technical details with me or restored vps, after all time I lose with them. But I'm happy with moving, that I can establish new architect I wanted from begining (with some advancements), instead running all things on single vps, and improve security significantly. 

I'm very sorry for about 120000 shorted urls we lose from the database, since I had only 2 month old backup locally. 

#### Things or two to learn from downtime

- If you have bad provider or you not happy with your current, run from them as fast as you can, even if that means you'll lose money
- Is better to spend extra money from beginning, especially if you already have architect idea in your head
- Github is your friend if you open-source project, you could use gh-pages and you can restore data from your github repository (for example Strong URL support was not present in backup, but I found a time to update github repository with it, and that saved a lot time)
- Cloudflare is not a magic pill, more above they report your hosting provider name to abuser. And if your hosting provider is small, it will be easy to DDoS provider network and break your website that way.

>We have provided the name of your hosting provider to the reporter. Additionally, we have forwarded this complaint to your hosting provider.
- You need to find a time to establish solution for local backups from your server. Any backups in cloud, AWS or with remote backups provider are not reliable and don't belong to you. You could use local backups on server and then [syncthing](https://syncthing.net/) for local backups from server if you under nat and with very unstable connection to remote server like me (when even ddns or ipv6 tunnel is not an option). Another option is backups to faster (or with better location) remote server and then local backups, for example (but you need to think about encryption that way).
- Is better not to use your real server ip even if you under CDN. Give your frontend ip to CDN. Don't store any project data on frontend. You don't need high performance server for frontend, just with fast internet in most use cases. Is better when its located physically in another place and under another provider.



