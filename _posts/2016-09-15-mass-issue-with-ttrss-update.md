---
title: Mass issue with Tiny Tiny RSS update
tags:
  - ttrss
  - tinytinyrss
  - rss
  - update
date: 2016-09-14
---
First of all this is not a problem with a Tiny Tiny RSS, but this a human problem, where people just too lazy to make updates.  
Beside since some version ttrss moved to [rolling release](https://tt-rss.org/forum/viewtopic.php?f=10&t=3262) model, but old version still reports that "there are no updates available". I wrote this post mostly for people I reported they have issues with ttrss installation. But if you not one I email, but you read this post, you should check your ttrss installation too.<!--more-->

- First of all you need install robots.txt in ttrss directory  
This will cover you installation from search robots. Without it you could find bad ttrss installation with for ex.:
`Tiny Tiny RSS : Preferences`, `ttrss prefs.php` or `rss prefs.php` keywords.

```bash
User-agent: *
Disallow:
```
- Next you probably need to backup opml with all you feeds
- After ttss [reinstallation](https://tt-rss.org/gitlab/fox/tt-rss/wikis/InstallationNotes) you need to [disable access to cache dirs](https://tt-rss.org/gitlab/fox/tt-rss/wikis/SecuringCacheDirectories)
- Next you need set password for administrator, create new user and import your feeds to user account
- And at the end you should setup cron job for ttrss update for ex.:

```bash
0 * * * * su -s /bin/sh www-data -c 'cd /var/www/ttrss/public_html && /usr/bin/git pull origin master'
```

Also, if your ttrss installation directory already known to google you need to move your installation to new directory, and you can set certificate auth if you want.






