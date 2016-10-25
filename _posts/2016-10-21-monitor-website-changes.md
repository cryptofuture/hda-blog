---
title: Monitor website content changes
tags:
  - website changes
  - python
  - email
  - monitor
  - monitor content changes
date: 2016-10-21
---
I was looking for website or tool to monitor website content changes. In my situation I was mostly interested in tool for monitoring new commits and releases. I used proprietary and online [UrlEye](https://urleye.com/) before, but idea to use 3-rd party tool for that was not in my personal favor. More above, I was not impressed with increasing number of false-positives and email delays. I started to look for open-source solution, but was not impressed with open-source solutions, until I run into [MailWebsiteChanges](https://github.com/Debianguru/MailWebsiteChanges) project.<!--more-->

#### What we already have
First of all before starting I want to mention that if you just need GitHub commits or releases notification you can use project rss feeds.  
`https://github.com/username/project/commits/master.atom.` and `https://github.com/username/project/releases.atom`. 

#### MailWebsiteChanges installation

```bash
sudo apt-get -y install python3-lxml python3-cssselect
cd /opt/
git clone https://github.com/Debianguru/MailWebsiteChanges
cd /opt/MailWebsiteChanges; mkdir changes
chown -R www-data:www-data /opt/MailWebsiteChanges/
```

#### Snippets
You can find some already made snippets here [wiki](https://github.com/Debianguru/MailWebsiteChanges/wiki/snippets)  
Snippets examples based on snippets I use: 

```python
# GitHub, new software release
{'shortname': 'GitHub, new release - projectname',
           'uri': 'https://github.com/username/project/tags.atom',
           'contentxpath': '//entry/title',
           'encoding': 'utf-8'},
           
# GitHub, new commits
{'shortname': 'GitHub, new commits - projectname',
           'uri': 'https://github.com/username/project/commits/master.atom',
           'contentxpath': '//entry/id',
           'encoding': 'utf-8'},
           
# SourceForge, new software release
{'shortname': 'SourceForge, new release - projectname',
           'uri': 'https://sourceforge.net/projects/projectname/',
           'contentxpath': '//section[@id=\'download_button\']//small',
           'encoding': 'utf-8'},  

# Mercurial, new software release
{'shortname': 'Mercurial, new release - projectname',
           'uri': 'http://url/projectname/tags',
           'contentxpath': '//tr[@class=\'tagEntry\']',
           'contentregex': '/projectname/rev/release-*.*.*',
           'encoding': 'utf-8'},  

# Mercurial, new commits
{'shortname': 'Mercurial, new commits - projectname',
           'uri': 'http://url/repos/projectname/rev/tip',
           'contentxpath': '//h3',
           'encoding': 'utf-8'},           

# Bazaar - Launchpad, new revision
{'shortname': 'Bazaar Launchpad, new revision - projectname',
           'uri': 'https://bazaar.launchpad.net/~projectname/projectname/trunk/files',
           'contentxpath': '//span[@class=\'breadcrumb\']',
           'encoding': 'utf-8'},
```

#### Configuration

```python
# config
os.chdir('/opt/MailWebsiteChanges/changes')
```

Some websites don't like MailWebsiteChanges user-agent, thats why I made some mwc script modifications.

> UPDATE. You don't need it since [50af2ea](https://github.com/Debianguru/MailWebsiteChanges/commit/50af2eabda1dadaa4208a3d7f06686f824d5f50d) commit.

```python
        titleregex = site.get('titleregex', '')
+       broken = site.get('broken', '')
        enc = site.get('encoding', defaultEncoding)

        ...
+        if broken != '':
+            file = urllib.request.urlopen(urllib.request.Request(uri,headers={'User-Agent':' Mozilla/5.0 (Windows NT 6.1; rv:38.0) Gecko/20100101 Firefox/38.0'}))
                else:
                    # open website
                    file = urllib.request.urlopen(uri)

```
That way you can use for ex. `'broken': 'true',` with broken website.



```bash
# cron example           
0 */3 * * * su -s /bin/sh www-data -c '/opt/MailWebsiteChanges/mwc.py'
```

#### Final thoughts
I'm very happy, to finally find exactly what I was looking. In this post I used MailWebsiteChanges to check new releases, but obviously you can use mwc to track changes on any site, and even track changes in your system with mwc.

```python
 {'shortname': 'lscmd',
           'uri': 'cmd://ls -l /home/pi',
           'contentregex': '.*Desktop.*'
}
```
More above, you can use MailWebsiteChanges to generate rss feed.















