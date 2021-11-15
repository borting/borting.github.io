---
layout: post
title: "How Overlayfs Deals with Linux Timestamp"
author: "Borting"
categories: journal
tags: [Docker, Linux]
image: chimei-museum.jpg
---

# mtime, atime, and ctime

Linux file has three timestamps:
* mtime (modified timestamp): the last time the contents of a file were modified
* atime (access timestamp): the last time a file was read
* ctime (changed timestamp): the metadata related to the file (e.g. permission or ownership) was changed

For example, the following command can change file timestamp
* Append a string to a file by `echo` command only changes mtime and ctime
* Use `cat` command to display a file only changes atime
* Use `touch` `touch -a` `touch -m` command to update atime+mtime, atime, mtime
* Use `chmod` command only update ctime

Check timestamps of a file
```shell
$ stat file

$ ls -l file	// mtime
$ ls -lu file	// atime
$ ls -lc file	// ctime
```

# Filesystem Mount Options for Timestamp

There some options control how atime of a file is updated when kernel mounting a file system.
For example,
* strictatime (strict atime): This option updates the access timestamp of files every time they’re accessed. 
* noatime (no atime): This option fully disables the access timestamps for files and directories from updating.
* relatime (relative atime): This option updates the access timestamp only if it was more than 24-hours old, or the previous one was older than the current modified or changed timestamps.
For more options, see [mount manpage](https://man7.org/linux/man-pages/man8/mount.8.html)

To check how mounted filesystems handle atime
```shell
$ cat /proc/mounts
```

On my computer, the `/` is mounted as `relatime` options.
```
/dev/sda1 / ext4 rw,relatime,errors=remount-ro,data=ordered 0 0
```


# Reference
* [Linux File Timestamps Explained: atime, mtime, and ctime](https://www.howtogeek.com/517098/linux-file-timestamps-explained-atime-mtime-and-ctime/)
