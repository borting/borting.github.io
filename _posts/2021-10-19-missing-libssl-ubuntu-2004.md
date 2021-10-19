---
layout: post
title: "Install libssl.1.0.0 on Ubuntu 20.04"
author: "Borting"
categories: journal
tags: [Linux, Docker]
image: Jokulsarlon.jpg
---

在 Ubuntu 20.04 的 docker image 上裝 [v4.4.140 kernel header](https://kernel.ubuntu.com/~kernel-ppa/mainline/v4.4.140/) 時, 遇到 package dependency 問題.
```
dpkg: dependency problems prevent configuration of linux-headers-4.4.140-0404140-generic:
 linux-headers-4.4.140-0404140-generic depends on libssl1.0.0 (>= 1.0.0); however:
  Package libssl1.0.0 is not installed.
```
但在 Ubuntu 20.04 時, openssl library 已經改包裝在 `libssl1.1` package 中了


# Solution

幸好 Ubuntu 的 libssl1.0.0 的 package 還有在維護.
到[這裡](http://security.ubuntu.com/ubuntu/pool/main/o/openssl1.0/)下載最新的 .deb 檔安裝就可以了.
```shell
$ wget	http://security.ubuntu.com/ubuntu/pool/main/o/openssl1.0/libssl1.0.0_1.0.2n-1ubuntu5.7_i386.deb
$ dpkg -i http://security.ubuntu.com/ubuntu/pool/main/o/openssl1.0/libssl1.0.0_1.0.2n-1ubuntu5.7_i386.deb
```

# Reference
* [Ubuntu 20.04: libssl.so.1.0.0: cannot open shared object file: No such file or directory](https://askubuntu.com/a/1331642)
