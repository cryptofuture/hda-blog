---
title: Using netdata with influxdb backend
tags:
  - netdata
  - influxdb
  - opentsdb
  - netdata backend
  - metrics
date: 2017-01-09
---
First of all currently netdata support two backends: graphite and opentsdb, and you can find actual information [here](https://github.com/firehol/netdata/wiki/netdata-backends).  
And the main thing you should know is that not actually means, that netdata support only [Graphite](https://graphite.readthedocs.io/en/latest/) or [OpenTSDB](http://opentsdb.net/), but every application with graphite plaintext format or opentsdb telnet format support. Here is a tricky part I can't call something telnet/plaintext as a protocol.<!--more-->  
Since I didn't knew this from beginning I started with OpenTSDB (no tags support in graphite).  
As a result I tried and tested OpenTSDB, [KairosDB](http://kairosdb.github.io/) and finished with [InfluxDB](https://www.influxdata.com/time-series-platform/influxdb/) in production.  

OpenTSDB was not easy to setup, since it needs [HBase](https://hbase.apache.org/), but compression only works, when HBase linked with [Hadoop](http://hadoop.apache.org/), but its not linked from beginning (surprise!). No packages, no init/systemd units, basically nothing. Anyway I made own [PPA for HBase](https://launchpad.net/~hda-me/+archive/ubuntu/hbase), so if you use Ubuntu Xenial and you can install it without extra hassle. Beside I made lxd container with OpenTSDB and [Grafana](http://grafana.org/) installed, and can find it [here](https://github.com/cryptofuture/lxd-image-opentsdb). But if you want to test/use it with netdata don't forget to add `tsd.core.auto_create_metrics = true` and add `opentsdb` to /etc/hosts, like `127.0.0.1	localhost opentsdb`.

#### netdata settings
As you can see I use OpenTSDB format and openvpn connection between netdata host and InfluxDB.  
You could find more information about backends in netdata [here](https://github.com/firehol/netdata/wiki/netdata-backends).

```
[backend]
    enabled = yes
    type = opentsdb
    destination = tcp:10.x.x.x%tun0:yourport
    data source = average
    prefix = yourhostname
    hostname = yourhostname
    update every = 10
    buffers on failures = 10
    timeout ms = 20000
```

#### InfluxDB settings

Here we configured opentsdb backend for netdata, disabled usage data statistics reporting to usage.influxdata.com and subscriber service, and enabled tls and authentication for InfluxDB.  
    
You can find [package repository](https://www.influxdata.com/package-repository-for-linux/), and information how [enable authentication and generate certificate](https://billyoverton.com/2016/05/30/smart-meter-installing-and-configuring-influxdb.html).  
Also you may be interesting in using own CA for certs. Check [this link](https://blog.hda.me/2016/08/27/cert-check-with-nginx.html) then. Basically with own CA usually you use `127.0.0.1` for InfluxDB, and local ip as grafana common name.

##### opentsdb backend

```
[[opentsdb]]
  enabled = true
  bind-address = ":yourport"
```

##### misc

```
reporting-disabled = true
bind-address = "127.0.0.1:8088"

[subscriber]
  # Determines whether the subscriber service is enabled.
  enabled = false
```

##### enabling tls for InfluxDB
```
[http]
  # Determines whether HTTP authentication is enabled.
  auth-enabled = true
  
  # Determines whether HTTPS is enabled.
  https-enabled = true

  # The SSL certificate to use when HTTPS is enabled.
  https-certificate = "/etc/influxdb/pathto.pem"

```

#### Grafana settings

You can install Grafana from [repository](http://docs.grafana.org/installation/debian/). Next part is too [add InfluxDB to Grafana](http://docs.grafana.org/datasources/influxdb/).
This is how my settings looks like:
![Grafana InfluxDB data source]({{site.url}}/images/post-images/grafana-data-source.png)

As you can see I run grafana on the same host with InfluxDB, use postgres (you can stay with defaults), use tls, and also I disabled some stats collecting.

```
[server]
# Protocol (http or https)
protocol = https

# The ip address to bind to, empty will bind to all interfaces
http_addr = local_ip

# The full public facing url you use in browser, used for redirects and emails
# If you use reverse proxy and sub path specify full url (with sub path)
root_url = https://local_ip:3000

# enable gzip
enable_gzip = true

# https certs & key file
cert_file = /pathto.crt
cert_key = /pathto.key

[database]
type = postgres
host = 127.0.0.1:5432
name = dbname
user = username
password = userpasswd

[session]
# Either "memory", "file", "redis", "mysql", "postgres", default is "file"
provider = memory

[analytics]
# Server reporting, sends usage counters to stats.grafana.org every 24 hours.
reporting_enabled = false

# disable gravatar profile images
disable_gravatar = true

[users]
# disable user signup / registration
allow_sign_up = false
```

#### Using netdata data in grafana

For example, you want to make own CPU Utilization chart:

* Add Row (in new dashboard)
* Select Graph, and edit it
* Graph - Metrics (second tab):
* A From `yourhostname.system.cpu.user` where host = yourhostname (tag)
* B From `yourhostname.system.cpu.softirq` where host = yourhostname (tag)
* C From `yourhostname.system.cpu.system` where host = yourhostname (tag)
* D From `yourhostname.system.cpu.iowait` where host = yourhostname (tag)
* General (first tab): type title `CPU Utilization`
* Axes (third tab): Left Y Unit percent (0-100)
* And chart is ready!

![CPU Utilization char]({{site.url}}/images/post-images/cpu_chart.png)

Also you download already pre-made dashboards for netdata from [my grafana account page](https://grafana.net/grafanauser)  
Just change `yourhostname` to your actual hostname. And as for lxd container, change `yourhostname` and `yourcontainername`.












