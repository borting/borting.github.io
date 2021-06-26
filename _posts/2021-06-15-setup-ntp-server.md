---
layout: post
title: "Setup NTP Service for Iperf Latency Test"
author: "Borting"
categories: journal
tags: [Linux]
image: Jokulsarlon.jpg
---

最近工作上需要用 iperf 測試 latency.
Survey 了一下發現只有 Linux 版的 iperf2 才有支援 latency test (iperf3 和 windows 版的 iperf2 都沒有).
此外, 還需要架設 NTP service 讓 hosting iperf client/server 的電腦同步時間, 算出來的 latency 才會準.
這篇紀錄一下要如何在 Ubuntu 上架設 NTP service.

# Pacakge Installation

On both server and client, install the following packages.
```bash
$ sudo apt install ntp ntpdate
```

NTP package 灌好後 Ubuntu 就會自己啟動 `nptd` 開始跟 remote NTP server 校時.

## Client-side Configuration

* 修改 NTP 設定 `/etc/ntp.conf`
```bash
# Mark all default NTP servers


```

## Server-side Configuration


# Conclusion

以前搶票的時候都要先連國家高速網路中心的 NTP server 校時, 沒想到這次居然要自己架 NTP server ... "Orz

# Reference

* [Ubuntu 16.04 NTP伺服器設置](https://codingnote.blogspot.com/2020/03/ubuntu-1604-ntp.html)
