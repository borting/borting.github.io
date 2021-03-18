---
layout: post
title: "Change Login Shell without Root Privilege"
author: "Borting"
categories: journal
tags: [Linux]
image: Jokulsarlon.jpg
---

最近接手同事的案子, 拿到一台 server 的登錄權限.
但登進去後發現 default shell 不是 `bash` 有點小崩潰 ... 趕緊想辦法換一下.

# chsh

在沒有 root 權限的情況下, 用 `chsh` 是最快的作法.
```bash
# Change default shell to bash
$ chsh -s /bin/bash
```

若要查詢系統安裝了哪些 shell.
```bash
$ cat /etc/shells
```

# Reference

* [Changing default shell in Linux](https://stackoverflow.com/a/13046283)
* [How to find list of available shells by command-line?](https://unix.stackexchange.com/a/140287)
