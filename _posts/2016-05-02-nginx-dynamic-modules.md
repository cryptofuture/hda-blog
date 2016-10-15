---
title: Nginx dynamic modules
tags:
  - nginx modules
  - dynamic modules
  - nginx
date: 2016-05-02
---

Nginx [dynamic modules](https://www.nginx.com/blog/dynamic-modules-nginx-1-9-11/) support was best thing ever happenned with nginx in last time.
This gives you great control and ability to load only modules you actually using.<!--more-->

#### How to use it
This pretty simple you need to install package with module you need, for example with:

```bash
sudo apt-get install nginx-module-naxsi
```
Add then add string to the top of /etc/nginx/nginx.conf (for example after pid) and reload nginx.

```bash
load_module modules/ngx_http_naxsi_module.so;
```
Pretty easy isn't it?  

I maintain [Nginx PPA](https://launchpad.net/~hda-me/+archive/ubuntu/nginx-stable) on launchpad, and since most modules dynamic now, you can ask me to add module you use to ppa.  
Github for ppa [here](https://github.com/cryptofuture/nginx-hda-bundle). 

> Fast-way: Pull request with changes, better if module will be as git submodule. Don't forget to change rules file and create install rules for module.  
 Slower way: Create issue request with module description and link to module, and I'll do it myself in spare time.
