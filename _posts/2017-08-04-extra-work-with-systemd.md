---
title: Extra work with systemd unit override
tags:
  - systemd
  - debian
  - ubuntu
  - unit rewrite
date: 2017-08-04
---
Let's say you want to override systemd unit. You typical way to do it will be copying original unit from `/lib/systemd/system/unit.service` to `/etc/systemd/system/`, editing unit and reloading systemctl with `systemctl daemon-reload`. And you will be very surprised, that your previous jobs or users still use `/lib/systemd/system/unit.service`. So you need to do `systemctl reenable unit@job` for every connection/user/job. And simple unit editing became a way longer task...
<!--more-->

#### Why this happens?

When you do `systemctl enable unit@job` systemd creates symlink from /etc/systemd/system/multi-user.target.wants/unit@job.service to /etc/systemd/system/unit.service and after you overrided default unit and done `systemctl daemon-reload` symlink to the old path still remains. `systemctl reenable` fixes it, but considering you may have many connections/users/jobs you can lose some time.

#### And what about dpkg

Usually on package upgrade dpkg asks: "What you want to do with updated config file" - rewrite, keep your old version, show differences or start shell. But this not apply to systemd units, and dpkg simply just rewrites systemd unit in `/lib/systemd/system/*` with new version. But you have some trick as package maintainer, you can place systemd unit into `/usr/lib/systemd/system/` and that way dpkg still treat unit as configuration file. This way, whenever user edit it, modified version will remain after update. And user still will able to create custom unit in `/etc/systemd/system/`. Maybe this not the official way to do things, but this works as intended even with people who had no time to read systemd docs.  
Will be nice to see some "right way to do" commentary on it.
