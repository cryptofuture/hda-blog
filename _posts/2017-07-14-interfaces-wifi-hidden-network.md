---
title: Hidden wifi in network/interfaces
tags:
  - wifi
  - debian
  - ubuntu
  - network interfaces
date: 2017-07-14
---
I had a little trouble connecting to hidden wpa2 psk with `network/interfaces`. First of all since network name is empty, you can't use `wpa_passphrase` to has with your real hidden name.  
And this a configuration I ended with:

```bash
auto wlan0
iface wlan0 inet dhcp
wpa-ssid NETNAME
wpa-scan-ssid 1
wpa-psk password
```
<!--more-->

As you can see the magic line is `wpa-scan-ssid 1` and without it you just will never find you hidden "" empty name Wi-Fi. Also I'm pretty sure you can hash password with empty("") ssid name, but I didn't tried it.
