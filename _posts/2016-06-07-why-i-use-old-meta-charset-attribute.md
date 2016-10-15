---
title: Old meta charset attribute with Firefox
tags:
  - firefox
  - charset
  - old bugs
date: 2016-06-07
---
I use `<meta http-equiv="Content-Type" content="text/html; charset=UTF-8">` instead `<meta charset="UTF-8">` on all my sites.<!--more-->
And well, I like HTML5 format more. But its a shame, bug [was reported in 2010](https://bugzilla.mozilla.org/show_bug.cgi?id=584285), and patch exist.  
But nobody wants fix it. And every time I open `Page Info` in Firefox (same in Nightly too) I see this empty meta tag.
