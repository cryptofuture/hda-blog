---
title: K-9 Mail Restore Password
tags:
  - android
  - base64
  - k-9 mail
  - email
  - password recovery
  - sqlite
date: 2013-12-07
---

#### Recovery process example
1. Grab preferences_storage database from /data/data/com.fsck.k9  
2. Open sqlite database preferences_storage table from database<!--more-->

    > Note: I used [sqlite database browser](http://sqlitebrowser.sourceforge.net/) available from GNU/Linux Ubuntu repositories.

3. Find and copy .transportUri value (its base64 decoded string)
4. Decode string with base64 built-in system decoder or [online decoder](http://www.base64decode.org/)

    > ```bash
#base64 decode
echo yourstringvalue== | base64 –decode
```
5. Use [hexdecoder](http://ddecode.com/hexdecoder/) on decoded string if you using long passwords, and previous string not helped you to fresh your head  
6. And finally your should figure out about hex symbols converting with [this table](http://www.nicolas-hoffmann.net/utilitaires/codes-hexas-ascii-unicode-utf8-caracteres-usuels.php)

#### Another method and tricks
- Enable [debugging and sensitive logging](https://github.com/k9mail/k-9/wiki/LoggingErrors) in the k9 client
    - Pass AUTH PLAIN XXX to `base64 -d`
    
- You can use this sql query to retrive transportUri

```sql
SELECT value FROM preferences_storage WHERE primkey LIKE “%transport%”;
```
- Sometimes you need use .storeUri instead .transportUri

> Thanks for comments on my [old blog note](https://cryptofuture.wordpress.com/2013/12/07/k-9-mail-password/), everyone!

