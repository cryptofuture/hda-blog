---
title: Reusing encryptfs /home
tags:
  - encryptfs
  - encrypted home directory
  - move encryptfs home
  - backup mounted encryptfs home
date: 2013-12-30
updated: 21-04-2016
---

If you need to reuse your encrypted home in another location – that's pretty easy.

1. You just need to copy your encrypted /home under new location
2. Then you need to run `ecryptfs-unwrap-passphrase /home/.ecryptfs/your_username/.ecryptfs/wrapped-passphrase` and type your old password<!--more-->

#### Login
In case you moved only .ecryptfs directory in your /home, you also need to make additional symlinks in your_username directory.

```bash
cd /home/your_username
ln -s /home/.ecryptfs/your_username/.Private .Private
ln -s /home/.ecryptfs/your_username/.ecryptfs .ecryptfs
ln -s /usr/share/ecryptfs-utils/ecryptfs-mount-private.desktop Access-Your-private-Data.desktop
ln -s /usr/share/ecryptfs-utils/ecryptfs-mount-private.txt README.txt
```

#### Backup
Sometimes you in situation when your encrypted user directory in use and mounted already. But you need some encryptfs home directory backups.  
Solution is pretty easy if you running backups with root access you can include `/home/.ecryptfs/your_username` directory in backup configuration.  
In case you running backups with user access only (for example with [Déjà Dup](http://www.howtogeek.com/108869/how-to-back-up-ubuntu-the-easy-way-with-dj-dup/)) you can include `/home/your_username/.Private` directory in your backup list.


