---
layout: post
title: "Disable IPv6 on Ubuntu Linux"
author: "Borting"
categories: journal
tags: [Linux]
image: Jokulsarlon.jpg
---

# Check IPv6 status

```shell
ip -6 addr show
```

# Temporarily Disable IPv6

```shell
sudo sysctl -w net.ipv6.conf.all.disable_ipv6=1
sudo sysctl -w net.ipv6.conf.default.disable_ipv6=1
sudo sysctl -w net.ipv6.conf.lo.disable_ipv6=1
```

# Permanently Disable IPv6

Edit `/etc/default/grub`
```shell
GRUB_CMDLINE_LINUX_DEFAULT="quiet splash ipv6.disable=1"
GRUB_CMDLINE_LINUX="ipv6.disable=1"
```

Update grub, then reboot
```shell
sudo update-grub
reboot
```

# GitHub Markdown Langauge Highlight Support

* [How to Disable IPv6 on Ubuntu Linux](https://itsfoss.com/disable-ipv6-ubuntu-linux/)
* [How to Enable or Disable IPV6 on Ubuntu Linux](https://ultahost.com/knowledge-base/enable-disable-ipv6-ubuntu-linux/)
