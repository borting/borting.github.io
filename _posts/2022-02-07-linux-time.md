---
layout: post
title: "Linux Timestamp: atime, mtime, ctime"
author: "Borting"
categories: journal
tags: [Linux]
image: Jokulsarlon.jpg
---

Linux 的檔案有三個 timestamp:
* atime: the time of last data access
* mtime: the time of last data modification
* ctime: the time the file status last changed

# Check File Timestamp

在 user space 可以用 `stat` 指令確認 file 的 timestamp.
Kernel 會將上述三個 timestamp 放在 struct stat 回傳.
```shell
$ stat [FILE]
```
# Update Timestamp by System Calls

下列 system calls 會更新 timestamps:
* [stat()](https://pubs.opengroup.org/onlinepubs/9699919799/functions/stat.html)
```
The stat() function shall update any time-related fields (as described in XBD File Times Update), before writing into the stat structure.
```
* [link()](https://pubs.opengroup.org/onlinepubs/9699919799/functions/link.html)
```
Upon successful completion, link() shall mark for update the last file status change timestamp of the file.
Also, the last data modification and last file status change timestamps of the directory that contains the new entry shall be marked for update.
```
* [mkdir()](https://pubs.opengroup.org/onlinepubs/9699919799/functions/mkdir.html)
```
Upon successful completion, mkdir() shall mark for update the last data access, last data modification, and last file status change timestamps of the directory.
Also, the last data modification and last file status change timestamps of the directory that contains the new entry shall be marked for update.
```
* [mkfifo()](https://pubs.opengroup.org/onlinepubs/9699919799/functions/mkfifo.html)
```
Upon successful completion, mkfifo() shall mark for update the last data access, last data modification, and last file status change timestamps of the file.
Also, the last data modification and last file status change timestamps of the directory that contains the new entry shall be marked for update.
```
* [mknod()](https://pubs.opengroup.org/onlinepubs/9699919799/functions/mknod.html#)
```
Upon successful completion, mknod() shall mark for update the last data access, last data modification, and last file status change timestamps of the file.
Also, the last data modification and last file status change timestamps of the directory that contains the new entry shall be marked for update.
```
* [open()](https://pubs.opengroup.org/onlinepubs/9699919799/functions/open.html#)
```
If O_CREAT is set and the file did not previously exist, upon successful completion, open() shall mark for update the last data access, last data modification, and last file status change timestamps of the file and the last data modification and last file status change timestamps of the parent directory.
```
* [symlink()](https://pubs.opengroup.org/onlinepubs/9699919799/functions/symlink.html)
```
Upon successful completion, symlink() shall mark for update the last data access, last data modification, and last file status change timestamps of the symbolic link.
Also, the last data modification and last file status change timestamps of the directory that contains the new entry shall be marked for update.
```

# Reference
* [Why does the change time(ctime) of a directory change when creating a new file in it?](https://stackoverflow.com/a/60451425)
* [POSIX.1-2017 -- File Times Update](https://pubs.opengroup.org/onlinepubs/9699919799/basedefs/V1_chap04.html#tag_04_09)
