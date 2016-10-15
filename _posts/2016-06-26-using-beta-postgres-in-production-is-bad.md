---
title: Never use PostgreSQL beta versions in production
tags:
  - postgres
  - postgresql
  - beta
date: 2016-06-26
---
I used postgres beta on one of my servers. Yesterday, I lost connection to my database completely. Unattended-upgrades was configured for updates from postgres apt repository long time ago.  
And since I got update automatically I missed this message from dialog:

![PostgreSQL Update Format Change]({{site.url}}/images/post-images/postgres-update.png)

I got this message after postgres restart attempt:

> The database cluster was initialized with CATALOG_VERSION_NO 201605051, but the server was compiled with CATALOG_VERSION_NO 201606171<!--more--> 
  
Main problem is apt overwrites old postgres package with new version everytime, and I mount /var/cache/apt/archives to tmpfs. Packages were removed from postgres apt repository and its mirrors ether.  
How I fixed it:  
I rebuilded postgres postgresql-9.6beta1 packages from postgres 9.6beta1 source. I used /debian folder from postgresql-9.6_9.6~beta2-1.pgdg80+1.debian.tar.xz (found in postgres apt repository).  
Nothing special here: changed changelog for lower version, used my pgp keys for signing, builded with pbuilder-dist.  
I figured out from apt history log, that I need to downgrade only three packages:

```
libpq5 (libpq5_9.6~beta1-2.pgdg80+1_amd64.deb)
postgresql (postgresql-9.6_9.6~beta1-2.pgdg80+1_amd64.deb)
postgresql-client (postgresql-client-9.6_9.6~beta1-2.pgdg80+1_amd64.deb)
```
After that I made pg_dumpall, installed and started stable v9.5 postgres on different post and made import. 
More interesting facts, you can't do pg_dump directly, for ex. this will fail:

```
pg_dumpall -p 5432 | psql -d postgres -p 5433
server version: 9.6beta1; pg_dumpall version: 9.5.3
aborting because of server version mismatch
```

After reconfiguring services to v9.5 post, I made upgrade to beta v9.6beta2, updated database cluster and I still run postgres beta on different port, but not for production anymore.  
From PostgreSQL wiki:  
    
> PostgreSQL data format can change between alpha releases - and occasionally even between beta releases if there's a really pressing need.
> If you need to access the old data, you will need to downgrade to the package version you were running before, pg_dump the data, and upgrade again.

Something in my protection:

* In time I was installing postgres 9.6, package was in postgres repository. Package was moved out of archive by default later.
* Most developers insulting you to install beta versions and in fact some software beta/git versions more stable than "stable" versions.
* I really wanted to try postgres 9.6.

Overall:  
You can run Postgres beta in production if you install every update yourself. But with automatic updates this is definitely not an option.
    


