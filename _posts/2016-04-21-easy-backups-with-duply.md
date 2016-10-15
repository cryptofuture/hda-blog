---
title: Easy backups with duply
tags:
  - backups
  - backup
  - duply
  - postgres
date: 2016-04-21
updated: 2016-08-22
updated2: 2016-08-24
---

Its not interesting to write topic about duply installation or configuration.  
I'll just provide you with links you could use.  
What I really want is to show you examples for server and desktop includes/excludes.   
And show you example pre/post scripts I use for postgres backup.<!--more-->

> By the way: **You do have backups, do you?**

In fact duply is very readable python script, and duplicity is very easy in use itself. But duply provide you with basic configuration you can apply to any target.  
More above you can even add several duply targets for backup in cron.   
Supported protocols list makes duplicity brilliant. And you can setup own backup to destination solution with duply/duplicity even on old and cheap machine.

#### Installation and Configuration
[Backup on Linux with duply](https://www.thomas-krenn.com/en/wiki/Backup_on_Linux_with_duply)  
[Duply](https://wiki.archlinux.org/index.php/Duply)  
[Data backups with Duplicity](https://wiki.zentyal.org/wiki/Data_backups_with_Duplicity)

#### Exclude file example

```bash
# Server example
+ /etc/*
+ /home/*
+ /root/*
+ /var/*
#+ /var/lib/postgresql/dbs/*
#+ /var/lib/pgbackrest/*
- **
```

```bash
# Desktop example
+ /etc/*
+ /home/.ecryptfs/*
+ /usr/local/bin/*
- **
```
> With new duplicity version you need to use `*`, or only folder structure will be included.

#### Postgres databases backup post/pre scripts

```bash
#pre file
#! /bin/bash
/root/.duply/target/cleardir >/dev/null 2>&1
sudo -u postgres /var/lib/postgresql/backups
```

```bash
#cleardir file
# We removing 5 days old backups here
#! /bin/bash
/usr/bin/find /var/lib/postgresql/dbs/* -mtime +5 -type f -delete
```

```bash
#post file
#! /bin/bash
/root/.duply/target/cleardir >/dev/null 2>&1
```

```bash
#backups file
#!/bin/bash
pg_dump db1 > /var/lib/postgresql/dbs/$(date +%F)-db1
pg_dump db2 > /var/lib/postgresql/dbs/$(date +%F)-db2
pg_dump db3 > /var/lib/postgresql/dbs/$(date +%F)-db3
pg_dumpall > /var/lib/postgresql/dbs/$(date +%F)-all
```

> Note: Postgres user has own .bash_history and postgresql store all your operations in postgres console.
  Add `\set HISTFILE /dev/null` to .psqlrc and `HISTSIZE=0 HISTFILESIZE=0` to .bashrc to disable it.
  
> Since databases became too large I switched to [pgBackRest](http://www.pgbackrest.org/) (full, incremental and differential backups support)

    

    


