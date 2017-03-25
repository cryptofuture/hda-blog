---
title: Fixing gala multi-monitor issue
tags:
  - gala
  - multi-monitor
  - broken
date: 2017-03-25
---
Basically, it's not an issue. But, I lost significant time with it.  Feature called `dynamic-workspaces`, with description:

```
When launching the window-switcher, gala iterates over this list and attempts to find a window matching the names. If it does, it will hide this window and fade it back in, once the the switcher is closed.
```

And this "feature" is enabled by default, and provents you from launching anything on secondary monitor, when you run application maximized or in fullscreen mode on primary.   
You can disable it with dconf: `pantheon -> desktop -> gala -> behavior -> dynamic-workspaces`
I just making this note, since: No I wasn't able to find it with search engine. Since, it's hard to find about [dynamic-workspaces](https://elementaryos.stackexchange.com/questions/10013/how-to-disable-the-empty-workspace-that-appears-when-switching-between-them) without searching exactly `gala dynamic-workspaces`.


