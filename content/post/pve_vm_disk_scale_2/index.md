---
title : "PVE中虚拟机硬盘在线扩容（LVM格式）- 单硬盘扩容"
description : "PVE中虚拟机硬盘在线扩容（LVM格式）- 单硬盘扩容"
date : "2024-04-15"
aliases : ["折腾"]
author : "飞天"
toc : true
---

[TOC]

## PVE下单磁盘扩容

上篇文章，我们讨论了在PvE下通过新增硬盘的方式进行扩容的方法



这篇文章，我们简单讲讲PvE下通过单盘扩容的方式进行扩容的方法



当前，两者的前提都是基于LVM格式下进行扩容。



## 案例

![整体思路](/home/mac/projects/blog/content/post/pve_vm_disk_scale/LVM.jpg)

### 查看现状（/dev/sda）

我有一个debian虚拟机，一开始只给了它10G的虚拟化硬盘：

```shell
$ sudo lsblk
NAME                  MAJ:MIN RM  SIZE RO TYPE MOUNTPOINT
sda                     8:0    0   30G  0 disk 
├─sda1                  8:1    0  487M  0 part /boot
├─sda2                  8:2    0    1K  0 part 
└─sda5                  8:5    0 29.5G  0 part 
  ├─debian--vg-root   254:0    0 28.6G  0 lvm  /
  └─debian--vg-swap_1 254:1    0  980M  0 lvm  [SWAP]
sr0                    11:0    1 1024M  0 rom
```

sda就是30G的盘，被分成了3个分区：sda1、sd2、sda5

sda1是引导分区，sda2不知道是什么作用，sda5是一个LVM类型的分区，我们详细分析一下这个分区：

```shell
$ df -h
Filesystem                   Size  Used Avail Use% Mounted on
udev                         2.0G     0  2.0G   0% /dev
tmpfs                        395M   16M  379M   5% /run
/dev/mapper/debian--vg-root   28G  3.7G   23G  14% /
tmpfs                        2.0G     0  2.0G   0% /dev/shm
tmpfs                        5.0M     0  5.0M   0% /run/lock
tmpfs                        2.0G     0  2.0G   0% /sys/fs/cgroup
/dev/sda1                    470M   83M  363M  19% /boot
```

可见，root那个LV被挂载到了/根目录，所以整个操作系统都在占用这个LV的空间，后续我们一旦发现硬盘耗尽，就需要对这个LV进行原地扩容即可。



### 扩容增加10G（/dev/sda）

进入PVE web控制台，增加10G，/dev/sda总容量增加至40G

![lvm_scale](/home/mac/Pictures/lvm_scale.png)

```
$  sudo fdisk -l
Disk /dev/sda: 40 GiB, 42949672960 bytes, 83886080 sectors
Disk model: QEMU HARDDISK   
Units: sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disklabel type: dos
Disk identifier: 0x987f6503

Device     Boot   Start      End  Sectors  Size Id Type
/dev/sda1  *       2048   999423   997376  487M 83 Linux
/dev/sda2       1001470 62912511 61911042 29.5G  5 Extended
/dev/sda5       1001472 62912511 61911040 29.5G 8e Linux LVM


Disk /dev/mapper/debian--vg-root: 28.6 GiB, 30668750848 bytes, 59899904 sectors
Units: sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes


Disk /dev/mapper/debian--vg-swap_1: 980 MiB, 1027604480 bytes, 2007040 sectors
Units: sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
```

### 使用part进行扩容

```
$ sudo parted /dev/sda

GNU Parted 3.2
Using /dev/sda
Welcome to GNU Parted! Type 'help' to view a list of commands.
(parted) print                                                            
Number  Start   End     Size    Type      File system  Flags
 1      1049kB  512MB   511MB   primary   ext2         boot
 2      513MB   31.2GB  31.7GB  extended
 5      513MB   32.2GB  31.7GB  logical                lvm
        
(parted) resizepart 2 100%                
(parted) resizepart 5 100%        
(parted) quit 

Information: You may need to update /etc/fstab.
```

**至此，磁盘扩容完毕！**

可以重新查看硬盘分区与容量情况

```
$ sudo fdisk -l
Disk /dev/sda: 40 GiB, 42949672960 bytes, 83886080 sectors
Disk model: QEMU HARDDISK   
Units: sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disklabel type: dos
Disk identifier: 0x987f6503

Device     Boot   Start      End  Sectors  Size Id Type
/dev/sda1  *       2048   999423   997376  487M 83 Linux
/dev/sda2       1001470 83886079 82884610 39.5G  5 Extended
/dev/sda5       1001472 83886079 82884608 39.5G 8e Linux LVM


Disk /dev/mapper/debian--vg-root: 28.6 GiB, 30668750848 bytes, 59899904 sectors
Units: sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes


Disk /dev/mapper/debian--vg-swap_1: 980 MiB, 1027604480 bytes, 2007040 sectors
Units: sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
```



/dev/sda2,/dev/sda5均已扩容到40G



后续讲讲zfs硬盘如何被多个虚拟机挂载使用



## 参考

[PVE下通过多硬盘扩容](https://flynx.dev/post/pve_vm_disk_scale/)

https://flynx.dev/post/pve_vm_disk_scale/