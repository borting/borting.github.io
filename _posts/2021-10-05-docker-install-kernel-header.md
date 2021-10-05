---
layout: post
title: "Install Specific Version of Kernel Header in Ubuntu Docker Container"
author: "Borting"
categories: journal
tags: [Docker, container]
image: chimei-museum.jpg
---

在編譯 libs/apps 的時候, 有時候需要 include kernel header.
但 container 和 host 共用 kernel, 沒有固定的 kernel 版本.
之前就遇過只要 host kernel 一更新, container 就找不到 kernel header 的問題, 後來要[在 container 中做一個假的 uname binary 騙過 configuration process]({% post_url 2021-02-25-fake-uname-inside-container %}) 才得以解決.
但如果需要安裝特定版本的 kernel header 的話, 就無法靠改 uname 解決, 只能想辦法手動安裝.

# Ubuntu Kernel Header Debian Packages

如果是在 Ubuntu Container 內的話, 可以從 [Ubuntu Kernel PPA](https://kernel.ubuntu.com/~kernel-ppa/mainline/) 直接找對應版本的 debian packages 下來安裝.
以下用在 Ubuntu 20.04 docker image 執行的 container 中安裝 v5.10.22 為例.

* Instal prerequisite packages
```bash
$ apt update
# apt install wget
$ apt install linux-base libelf1 kmod
```

* Download kernel header from [Ubuntu Kernel PPA](https://kernel.ubuntu.com/~kernel-ppa/mainline/)
```bash
$ wget -c https://kernel.ubuntu.com/~kernel-ppa/mainline/v5.10.22/amd64/linux-headers-5.10.22-051022_5.10.22-051022.202103090631_all.deb
$ wget -c https://kernel.ubuntu.com/~kernel-ppa/mainline/v5.10.22/amd64/linux-headers-5.10.22-051022-generic_5.10.22-051022.202103090631_amd64.deb
```

* Install kernel header
```bash
$ dpkg -i *.deb
```

* Check installation results
```bash
$ ll -R /lib/modules/
```

# Reference
* [How to Install Linux Kernel 5.10 LTS in Ubuntu/Mint](https://ubuntuhandbook.org/index.php/2020/12/install-linux-kernel-5-10-ubuntu-linux-mint/)
* [Ubuntu Kernel PPA](https://kernel.ubuntu.com/~kernel-ppa/mainline/)
