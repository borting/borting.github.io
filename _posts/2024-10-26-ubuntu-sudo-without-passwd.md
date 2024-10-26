---
layout: post
title: "Run sudo without Password on Ubuntu"
author: "Borting"
categories: journal
tags: [Linux]
image: Jokulsarlon.jpg
---

# Modification

編輯 `/etc/sudoers`, 將
```C
# Allow members of group sudo to execute any command
%sudo   ALL=(ALL:ALL) :ALL
```
改成
```C
# Allow members of group sudo to execute any command
%sudo   ALL=(ALL:ALL) NOPASSWD:ALL
```
