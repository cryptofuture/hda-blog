---
title: Insecure Firefox Security Addons
tags:
  - firefox
  - addons
  - security
date: 2014-01-19
updated: 2016-04-20
---

#### Searching for fair trade

Extra security measures can break your security faster instead helping. When you testing new browser addon your should always check what addon actually does, actually sending, check addon source code and addon compatibility with browser and another addons. <!--more-->  
Also you can look into addon Privacy Policy and service TOS, but most likely that already means that addon is not open-source and it will use third-party for its functional. Sometimes its just enough to check addon author profile or search for *addon_name* in search engine to find something bad. Also if have a lot addons in use, any addon with update can break something or even transform to bad addon.  
In best case addon is open-sourced and even available on github. But even in bad case you can unpack addon and check source.

> I hope to update this list. But it pretty obvious that vpn services addons and *"check website in security list before visiting"* addons will be ignored, since most of them are bad for you security by design.

#### Addons

* [Random Agent Spoofer](https://addons.mozilla.org/en-US/firefox/addon/random-agent-spoofer/)

Addon idea is not bad. But addon draw extra attention to you. If you used any analytics collection system like [piwik](http://piwik.org/) you will understand me.  
Addon comes with exotic default user-agent string. And user-agent switching is good only with ip changing and cookies removing. Also addon ignores platform values (like buildID, oscpu, navigator.system, navigator.version) when switching your user-agent.

* [FireGloves](http://fingerprint.pet-portal.eu/files/firegloves-1.2.3.xpi)

FireGloves was great addon but its [outdated](http://pet-portal.eu/blog/read/533/?set_language=eng). FireGloves addon works partly with Firefox. It leaks your original user-agent and builtin user-agent string is outdated too. And more above Firegloves sets some parameters and helps you be identified.

* [Ghostery](https://addons.mozilla.org/en-US/firefox/addon/ghostery/)

By the way popular addon. Addon have good open-source alternative [Disconnect](https://addons.mozilla.org/en-US/firefox/addon/disconnect/). But with last Firefox versions you don't need and can just use default browser tracking protection (basic or strict list equal to Disconnect list).  
Ghostery have sneaky Private Policy and have some issues, when addons preference could be reseted and all statics will be transfered to third-party automatically. And they even store data about every list update. Have no idea why you need use it, when tracking protection already build-in browser. 


