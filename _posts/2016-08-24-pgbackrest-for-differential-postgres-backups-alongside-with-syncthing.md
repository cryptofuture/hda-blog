---
title: Differential postgres backups alongside with syncthing
tags:
  - backups
  - backup
  - postgres
  - pgbackrest
  - syncthing
date: 2016-08-24
updated: 2016-10-15
---

Since postgres databases became too big, I started to search for differential and incremental backups solution for PostgreSQL.
Main problem is postgresql WALs tricky in maintenance. And I searched for something that will play nice with duply/duplicity.  
Thats how I discovered [pgBackRest](http://www.pgbackrest.org/).<!--more-->  
I made [PPA](https://launchpad.net/~hda-me/+archive/ubuntu/pgbackrest) for pgBackRest and thats an example how I deal with it:

```bash
#: /etc/pgbackrest.conf
[global]
repo-path=/var/lib/pgbackrest

[main]
db-path=/var/lib/postgresql/9.5/main
db-port=5432
retention-full=2
retention-diff=3
retention-archive-type=diff
```

```bash
#: /etc/postgresql/9.5/main/postgresql.conf (changes only)
archive_command = 'pgbackrest --stanza=main archive-push %p'
archive_mode = on
max_wal_senders = 3
wal_level = hot_standby
```

```bash
# cron example
0 2 * * * su -s postgres -c '/usr/bin/pgbackrest --type=full --stanza=main backup'
0 */4 * * * su -s postgres -c '/usr/bin/pgbackrest --type=diff --stanza=main backup'
```

We need to run syncthing under postgres user, since we need access to backups. Its easy to configure syncthing, using web-interface.

```bash
systemctl enable syncthing@postgres.service
systemctl start syncthing@postgres.service
```

pgBackRest is a lot more complex, and even support remote backups, but thats how I prefer to do things in my environment.  
I prefer to collect backups data from several hosts with duply/duplicity on one host, and then synchronize all backups locally with syncthing.  
[Syncthing](https://syncthing.net/) is pretty rock-solid and user-friendly, and the best thing I like about it, is ability to sync data on pretty decent speed even with highly NATed  and closed locations.



