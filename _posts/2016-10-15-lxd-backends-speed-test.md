---
title: Selecting best backend for LXD
tags:
  - lxd
  - lxc
  - btrfs
date: 2016-10-15
---
#### How it started
I detected crazy iowait with all read/write operations. Things was reasonable with a few containers, but after a while containers count grow, and even read/write limits in most loaded containers didn't make any difference. And after I several simple dd read/write tests with `sync; dd if=/dev/zero of=tempfile bs=1M count=1024; sync` in container I was shocked. It was 15.4 MB/s in ZFS separated partition. While on raw xfs filesystem on same SSD I had near 95 MB/s!  
Next thing I tested, was LXD on another production machine with plain(directory) backend. And it was only 1-2% difference in speed vs raw filesystem. After that I disabled prefetch for ZFS with `echo 1 > /sys/module/zfs/parameters/zfs_prefetch_disable`, but it didn't helped things, and I got 22.2 MB/s on zfs backend. This was the sign to move small server out of ZFS. <!--more-->

Storage backends supported functions from [storage-backends.md](https://github.com/lxc/lxd/raw/master/doc/storage-backends.md):

Feature                                     | Directory | Btrfs | LVM   | ZFS
:---                                        | :---      | :---  | :---  | :---
Optimized image storage                     | no        | yes   | yes   | yes
Optimized container creation                | no        | yes   | yes   | yes
Optimized snapshot creation                 | no        | yes   | yes   | yes
Optimized image transfer                    | no        | yes   | no    | yes
Optimized container transfer                | no        | yes   | no    | yes
Copy on write                               | no        | yes   | yes   | yes
Block based                                 | no        | no    | yes   | no
Instant cloning                             | no        | yes   | yes   | yes
Nesting support                             | yes       | yes   | no    | no
Restore from older snapshots (not latest)   | yes       | yes   | yes   | no
Storage quotas                              | no        | yes   | no    | yes

#### Test begins

I tested directory (plain) backend in separated partition on same SSD, mounted under /var/lib/lxd and as whole / partition with XFS and EXT4 filesystems. BTRFS in separated partition and as part of /. LVM with XFS and EXT4 filesystems. I also made tests using different mount options. However I was not able to test LVM backend with `storage.lvm_mount_options` since is not in repository lxd version, yet.

Details:

```
LXD version: 2.0.4
SSD: SanDisk SDSSDRC032G
Kernel: 4.4.0-40-generic
CPU: Intel(R) Atom(TM) CPU  230   @ 1.60GHz
```

How I tested: `sync; dd if=/dev/zero of=tempfile bs=1M count=1024; sync` 4 times and  
`fio --randrepeat=1 --ioengine=libaio --direct=1 --gtod_reduce=1 --name=test --filename=test --bs=4k --iodepth=64 --size=1G --readwrite=randrw --rwmixread=75` 2 times, middle values in the table.

Options                                   |    Raw (host)              | 
:---                                      |:---      |                 |
                                          | dd (MB/s)| fio (runt, msec)|  
                                          |---       |---              |
/, xfs, noatime,nobarrier                 | 92       |156416           | 
/, btrfs, noatime,ssd,nobarrier           | **94**   |**116566**       | 
/, btrfs, noatime,ssd,nobarrier,nodatacow | 88       |151841           |
/var/lib/lxd, xfs, noatime,nobarrier      | 93       |154046           | 
/var/lib/lxd, ext4, noatime,nobarrier     | 92       |168451           |
/var/lib/lxd, btrfs, noatime,ssd,nobarrier| 89       |136560           |

Options                                   |    Directory (in container)| 
:---                                      |:---      |                 |
                                          | dd (MB/s)| fio (runt, msec)|  
                                          |---       |---              |
/, xfs, noatime,nobarrier                 | **86**  |**164339**        | 
/var/lib/lxd, xfs, noatime,nobarrier      | 76       |170480           | 
/var/lib/lxd, ext4, noatime,nobarrier     | 83       |172358           |

Filesystem|    LVM  (in container)     | 
:---      |:---      |                 |
          | dd (MB/s)| fio (runt, msec)|  
          |---       |---              |
xfs       | 51       |171786           | 
ext4      | **54**   |**156157**       | 

Options                                   |    Btrfs (in container)    |
:---                                      |:---      |                 |
                                          | dd (MB/s)| fio (runt, msec)|  
                                          |---       |---              |
/, btrfs, noatime,ssd,nobarrier           | **94**   |**118584**       | 
/var/lib/lxd, btrfs, noatime,ssd,nobarrier| 87       |138856           |

#### Results and Conclusions
BTRFS shows best values and best performance on SSD. Disabling COW will make things worse for btrfs.  
There is some slight difference in read/write performance vs host. Without tweaking LVM backend mount options, ext4 shows better results, then xfs. ZFS performance is awful. There no reason to mess with ZFS, unless you build fat zraid. Directory is good choice for production with less than 5 containers count, or if you don't need to move and create new containers frequently. If you don't like BTRFS, LVM is second best choice.

#### BTRFS Tweaks
[btrfstune](https://btrfs.wiki.kernel.org/index.php/Manpage/btrfstune) mostly useful if you need to enable some option, when it was not activated on formating time. Partition needs to be unmounted, to use.  
`btrfs filesystem balance /` for btrfs balance/rebalance, could be useful after enabling skinny-metadata.  
`btrfs quota enable /` to enable quotas, need for quotas in containers.   











