---
layout: post
title: "Use neconsole to Debug Linux Kernel Panic"
author: "Borting"
categories: journal
tags: [Linux]
image: Jokulsarlon.jpg
---

在開發 Linux driver 時, kernel panic 是家常便飯.
在一般的 embedded system 上通常會有接出 console 讓我們在另外一台電腦上紀錄 panic 訊息.
但如果是在一般 PC 上開發, 沒有 console, kernel panic 時又來不急把訊息寫到 disc (e.g. `/var/log/syslog` or `/proc/kmesg`) 的話, 會無法 debug.
為了解決這個問題, Linux 的 developers 開發了 `netconsole` 這個 module, 將 local 的 `dmseg` 透過 UDP 送到另一台電腦上, 就可以紀錄到 kernel panic 瞬間的訊息了.

# Check netconsole support

```shell
grep NETCONSOLE /boot/config-*
ls /lib/modules/`uname -r`/kernel/drivers/net/netconsole.*
```

如果有出現下面訊息就代表有支援
```shell
CONFIG_NETCONSOLE=m
CONFIG_NETCONSOLE_DYNAMIC=y
/lib/modules/5.15.0-122-generic/kernel/drivers/net/netconsole.ko
```

# Use netconsole

兩台主機要以 ethernent 相連, 並且設定好 IPv4 address

在要 debug 的主機上 insert netconsole module, 並且將 kernel debug level 調高到 `KERN_DEBUG`
```shell
# L-Port - Local port
# L-IP - Local system IP
# L-Interface - Local system interface
# R-Port - Remote System udp port
# R-IP - Remote System IP
# MAC - Remote System Mac
sudo modprobe netconsole L-Port@L-IP/L-Interface,R-Port@R-IP/MAC
sudo sysctl -w kernel.printk="7 4 1 7"

# Example
sudo modprobe netconsole netconsole=6665@10.10.10.122/eth0,6666@10.10.10.123/00:11:22:33:44:55
sudo sysctl -w kernel.printk="7 4 1 7"
```

在接收 debug message 的主機上, 用 `nc` 開啟 UDP socket server, 並將收到的 dmsg 寫入檔案
```shell
# R-Port - Remote System udp port
nc -l -u -p R-Port | tee -a FILE

# Example
nc -l -u -p 6666 | tee -a ./netconsole.log
```

# Reference
* [netconsole.txt](https://www.kernel.org/doc/Documentation/networking/netconsole.txt)
* [netconsole](https://datahunter.org/netconsole)
