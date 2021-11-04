---
layout: post
title: "Setup Software RAID-1 on Ubuntu 20.04"
author: "Borting"
categories: journal
tags: [Linux]
image: Jokulsarlon.jpg
---

紀錄一下如何在 Linux 上架 RAID-1.

# Create RAID-1 for Non-root Directory

假設電腦有三顆 SSD,
* /dev/nvme0n1: 256 GB, 已安裝 Ubuntu 20.04, /dev/nvme0n1p2 掛載在 /
* /dev/nvme1n1:   1 TB, 準備做 RAID-1, 掛在 /data
* /dev/nvme2n1:   1 TB, 準備做 RAID-1, 掛在 /data

## Required Packages

* Install mdadm (Multiple Devices Admin)
```shell
$ sudo apt install mdadm
```

## Disk Status Check

* List block devices
```shell
$ sudo lsblk
```

* Display the partition table
```shell
$ sudo parted -a optimal /dev/nvme1n1 print
```

## Disk Cleanup

* Change to root
```shell
$ sudo -i
```

* Wipe entire disk:
```shell
$ dd if=/dev/nvme1n1 of=/dev/sdX bs=1M
$ dd if=/dev/nvme2n1 of=/dev/sdX bs=1M
```

* Or, Wipe sidk using wipefs:
```shell
$ umount /dev/nvme1n1p?; wipefs --all --force /dev/nvme1n1p?; wipefs --all --force /dev/nvme1n1
$ umount /dev/nvme2n1p?; wipefs --all --force /dev/nvme2n1p?; wipefs --all --force /dev/nvme2n1
```

## Disk Partition

* Create GUID partition table (GPT)
```shell
$ gdisk /dev/nvme1n1
$ gdisk /dev/nvme2n1
```
  * Enter `o` for new empty GUID partition table (GPT)
  * Enter `y` to confirm your decision
  * Enter `n` for new partition
  * Enter for default of first partition
  * Enter for default of the first sector
  * Enter for default of the last sector
  * Enter `fd00` for Linux RAID type
  * Enter `w` to write changes
  * Enter `y` to confirm your decision

* Examine disk status and should report `(type ee)`
```shell
$ mdadm --examine /dev/nvme1n1 /dev/nvme2n1
```

* Examine the first partition of the disk and should report `No md superblock detected`
```shell
$ mdadm --examine /dev/nvme1n1p1 /dev/nvme2n1p1
```

## RAID-1 Creation

* Create the RAID-1 array
```shell
$ mdadm --create /dev/md0 --level=1 --raid-devices=2 /dev/nvme1n1p1 /dev/nvme2n1p1 
```

* Check the array is created and synced
```shell
$ cat /proc/mdstat
$ mdadm --detail /dev/md0
```

## Filesystem Creation

* Format /dev/md0 as ext4
```shell
$ mkfs.ext4 -m 0 -E lazy_itable_init=0,lazy_journal_init=0 /dev/md0
```

* Check disk status
```shell
$ sudo parted -a optimal /dev/md0 print
```

* Update mdadm config
```shell
$ /usr/share/mdadm/mkconf | tee /etc/mdadm/mdadm.conf
```

## Disk Mount

* Mount disk temporarily
```shell
$ mkdir -p /data
$ mount /dev/md0 /data
```

* Get disk's UUID
```shell
$ blkid /dev/md0
```

* Edit /etc/fstab and add an entry for file system information 
```
UUID=<the UUID you have in the clipboard>    /data    ext4    defaults    0 2
```

* Update initramfs and reboot
```shell
$ update-initramfs -u -k all
$ reboot
```

# Create RAID-1 for Root Directory

需要使用 Ubuntu server image 安裝, 可參考 [Looking to create a Software RAID 1 setup for your 2-disk server on Ubuntu Server 20.04?](https://gist.github.com/fevangelou/2f7aa0d9b5cb42d783302727665bf80a)

# Intel Rapid Storage Technology

Intel Rapid Storage Technology (RST) 是一種 firmware RAID, 依照 Ubuntu 官方的[說法](https://help.ubuntu.com/rst/), Linux 可能可以用 Intel RST, 也可能不行 (WTX ...).
最好的方法是把 Intel RST 關閉.

這一篇 [Reddit 的文章](https://www.reddit.com/r/linuxquestions/comments/no4m0f/comment/gzyi0wt/?utm_source=share&utm_medium=web2x&context=3)有說 Intel 有 submit RST 相關的 code 到 Linux kernel, 但被[拒絕](https://lore.kernel.org/linux-pci/20190620061038.GA20564@lst.de/T/)了.
所以至今, Intel RST 基本上是無法完全 Support 的.
所以還是改用 Software RAID 吧.

# Reference
* [How to wipe a hard drive clean in Linux](https://how-to.fandom.com/wiki/How_to_wipe_a_hard_drive_clean_in_Linux)
* [mdadm RAID implementation with GPT partitioning](https://unix.stackexchange.com/a/320330)
* [How to regenerate the boot configuration for mdadm](https://sleeplessbeastie.eu/2018/01/29/how-to-regenerate-the-boot-configuration-for-mdadm/)
* [Setup Software RAID on Ubuntu 20.04](https://kifarunix.com/setup-software-raid-on-ubuntu-20-04/)
* [SUSE Storage Administration Guide - Software RAID](https://documentation.suse.com/en-us/sles/12-SP4/html/SLES-all/part-software-raid.html) ([中文翻譯](https://documentation.suse.com/zh-tw/sles/12-SP4/html/SLES-all/part-software-raid.html))
* [A guide to mdadm](https://raid.wiki.kernel.org/index.php/A_guide_to_mdadm)
* [/etc/fstab的dump與pass](https://2formosa.blogspot.com/2018/07/fstab-dump-pass.html)
