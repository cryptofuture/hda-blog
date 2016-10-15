---
title: Tor & I2P - Privoxy and PAC examples
tags:
  - privoxy
  - pac
date: 2014-02-03
---

#### Privoxy I2P and TOR examples

Forward any URL ending in .i2p through I2P and forward all remaining requests through Tor

```bash
forward .i2p localhost:4444
forward-socks5 / localhost:9050 .
```
<!--more-->
Use I2P as the primary network and the Tor network to access Tor specific resources

```bash
forward-socks5 .onion localhost:9050 .
forward / localhost:4444
```

Direct access host:port and use own proxy for pastebin

```bash
forward 10.0.3.156:10484 .
forward-socks5  .pastebin.com 192.0.0.5:4538 .
```

#### PAC I2P and TOR example

```bash
function FindProxyForURL(url, host)
{
//I2P
var I2P="PROXY 127.0.0.1:4444";
//TOR - connections to tor network
var TOR="PROXY 127.0.0.1:9050";
//TOR Proxy - tor as proxy
var TOR2="SOCKS 127.0.0.1:9050"
//Alternative Proxy - your socksv5 proxy for trusted sites
var CRYPTO="SOCKS 127.0.0.1:port";
//Direct connection without a proxy
var DIR="DIRECT";
 
//Redirect .I2P to I2P proxy
if (shExpMatch(host,"*.i2p"))
{
return I2P;
}
 
//Redirect Crypto
if (
shExpMatch(host,".site.com") ||
shExpMatch(host,".site.org") ||
shExpMatch(host,".google.com") ||
shExpMatch(host,"ip") ||
shExpMatch(host,".site.org") ||
shExpMatch(host,".mail.site.com"))
{
return CRYPTO;
}
 
//Directly
if (
shExpMatch(host,".site.com") ||
shExpMatch(host,".paypal.com") ||
shExpMatch(host,".store.steampowered.com") ||
shExpMatch(host,"127.0.0.1") ||
shExpMatch(host,".site.org"))
{
return DIRECT;
}
 
//Redirect .onion to TOR proxy
if (shExpMatch(host,"*.onion"))
{
return TOR;
}
 
//Redirect  to TOR proxy
if (shExpMatch(host,"*"))
{
return TOR2;
}
}
```
