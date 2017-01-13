---
title: Postgres replication repmgr and pglogical
tags:
  - repmgr
  - pglogical
  - logical replication
date: 2017-01-13
---
Is not hard to setup postgres replication using repmgr. But usually you looking for automatic failover solution instead of setup instructions.  
And here is the best part: easy, cheap and finished solution doesn't exist, yet. But could appear very soon. Best HA solution to the date is to use corosync, pacemaker and [PAF](https://dalibo.github.io/PAF/) but you need minimum three independent machines to setup it (two slaves and one master). <!--more--> Yes, you could setup two machines (master-slave), but in this case solution will be not much different from repmgr. Repmgrd automatically switch from master to slave, as new a new master, but you need to restore old master manually.  
And usually that means [pg_basebackup](https://www.postgresql.org/docs/9.5/static/app-pgbasebackup.html) from beginning. And beside in three machines setup with corosync/pacemaker you [STONITH](https://en.wikipedia.org/wiki/STONITH), and if you done this in expensive way, this is not just postgres port filtering, but real machine kill. And after it manual old master restoring from the beginning. And its not enough to just use three VPS from the same provider (even in different locations). Or in real bad situation you will lose all your cluster. And three really independent machines are NOT CHEAP! And after your first master death, you get same master-slave setup you can achieve with repmgr.  

There is a problem with pgpool/pgbouncer too. No matter how big your cluster, if you have only one bouncer... To lose bouncer means to lose all cluster in that case. And as for DNS records, is not cool to have more than two A records, two records means a bigger chance to receive double abuse letters. And to lose account with some providers. Master-master as postgres-bdr is not a magic bullet too, since its only made for old postgres 9.4 and data could became inconsistent.

Pglogical could become that cheap, easy and finished automatic failover solution. In fact pglogical already easy to setup, but it lacks automatic failover. However, after repmgr or another solution will be compatible pglogical. It will be win-win solution, where you will be able to restore provider without database restoring from beginning. And this means provider could be restored as automatically as subscriber. Beside I really like pglogical database to database way.  

But enough talks, i'll show repmgr configuration example for failover, and then will start with pglogical.

#### repmgr/repmgrd settings
Don't forget to activate repmgrd, in Debian/Ubuntu that will be `REPMGRD_ENABLED=yes` in `/etc/default/repmgrd` and `systemctl enable/start repmgrd`
In case, you have no idea how make base repmgr setup check for ex. this [link](https://edwardsamuel.wordpress.com/2016/04/28/set-up-postgresql-9-5-master-slave-replication-using-repmgr/) or better [offical readme](https://github.com/2ndQuadrant/repmgr)

```sh
# An arbitrary name for the replication cluster;
# This must be identical on all nodes
cluster = 'somecluster'
 
# A unique integer identifying the node
node = 1
 
# A unique string identifying the node;
# Avoid names indicating the current replication role like 'master' or 'standby'
# as the server's role could change.
node_name = 'node1'
 
# A valid connection string for the repmgr database on the current server.
conninfo = 'host=ip user=user dbname=db'
failover=automatic
promote_command='repmgr standby promote -f /etc/repmgr.conf'
follow_command='repmgr standby follow -f /etc/repmgr.conf'
reconnect_attempts=3
reconnect_interval=5
logfile='/var/log/postgresql/repmgr.log'
# Use replication slot if you enable replication slots in
# PostgreSQL configuration.
use_replication_slots = 1
pg_bindir=/usr/lib/postgresql/9.6/bin
```

```sh
# An arbitrary name for the replication cluster;
# This must be identical on all nodes
cluster = 'somecluster'

# A unique integer identifying the node
node = 2

# A unique string identifying the node;
# Avoid names indicating the current replication role like 'master' or 'standby'
# as the server's role could change.
node_name = 'node2'

# A valid connection string for the repmgr database on the current server.
conninfo = 'host=ip user=user dbname=db'
failover=automatic
promote_command='repmgr standby promote -f /etc/repmgr.conf'
follow_command='repmgr standby follow -f /etc/repmgr.conf'
reconnect_attempts=3
reconnect_interval=5
logfile='/var/log/postgresql/repmgr.log'
# Use replication slot if you enable replication slots in
# PostgreSQL configuration.
use_replication_slots = 1
pg_bindir=/usr/lib/postgresql/9.6/bin
```

#### pglogical setup
[Repositoty setup](https://2ndquadrant.com/de/resources/pglogical/pglogical-installation-instructions/)  
If you using default `postgresql.conf` you could include configuration for example in logical.conf with `include 'logical.conf'`.  
Here we changed pg_hba.conf, created replication user, created provider and subscriber nodes, and done subcription to databasetoreplicate.  
As you can see I use database-based naming for nodes, since you can replicate mupliple databases.

##### Both on provider and on subscriber
```sh
:logical.conf
max_wal_senders = 10
max_replication_slots = 10
max_worker_processes = 10
shared_preload_libraries = 'pglogical'
track_commit_timestamp = on
wal_level = 'logical'
listen_addresses = '*'
# When track_commit_timestamp is disabled, the only allowed value is apply_remote
track_commit_timestamp = on
```

```sql
psql provider:
CREATE ROLE replication WITH SUPERUSER REPLICATION LOGIN ENCRYPTED PASSWORD 'yourhardproviderpassword';
```

```sql
psql subscriber:
CREATE ROLE replication WITH SUPERUSER REPLICATION LOGIN ENCRYPTED PASSWORD 'yourhardsubscriberpassword';
```

```sql
:pg_hba.conf
host    databasetoreplicate   replication     subscriber_ip/32            md5
host    databasetoreplicate   replication     provider_ip/32            md5
host    replication   replication     subscriber_ip/32            md5
host    replication   replication     provider_ip/32            md5
```

##### On provider

```sql
psql:
\c databasetoreplicate
CREATE EXTENSION pglogical;

SELECT pglogical.create_node(
    node_name := 'databasetoreplicate',
    dsn := 'host=provider_ip port=5432 user=replication password=yourhardproviderpassword dbname=databasetoreplicate'
);

SELECT pglogical.replication_set_add_all_tables('default', ARRAY['public']);
```

##### On subscriber

```sql
psql:
CREATE databasetoreplicate name;
CREATE USER newusernamefordb WITH ENCRYPTED PASSWORD 'newpasswordtodbcopy';
ALTER DATABASE databasetoreplicate OWNER TO newusernamefordb;

\c databasetoreplicate
CREATE EXTENSION pglogical;

SELECT pglogical.create_node(
    node_name := 'databasetoreplicate-hostname',
    dsn := 'host=subscriber_ip port=5432 user=replication password=yourhardsubscriberpassword dbname=databasetoreplicate'
);

SELECT pglogical.create_subscription(
    subscription_name := 'subscription_to_databasetoreplicate',
    provider_dsn := 'host=provider_ip port=5432 user=replication password=yourhardproviderpassword dbname=databasetoreplicate'
);
```

Let's say you want to see your subscription details next, execute this on subscriber (you can find set name here):

```sql
psql:
\c databasetoreplicate
select * FROM pglogical.show_subscription_status();
```

And now, let's say our old provider down, and we want to remove subscription:

```sql
psql:
\c databasetoreplicate
select pglogical.alter_subscription_remove_replication_set('subscription_to_databasetoreplicate', 'set_from_pglogical.show_subscription_status');
select pglogical.drop_subscription('subscription_to_databasetoreplicate');
select pglogical.drop_node('databasetoreplicate-hostname');
```

And you can repeat steps above to subscribe our old provider to our new provider(old subscriber) as subscriber.
Basically, it will be win-win, if someone will automate failover.
And yes, I tried both side subscription, and it didn't work for me (should be something like master-master that way)




