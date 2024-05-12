---
layout: post
title: "Enable Keychron Function Keys on Linux"
author: "Borting"
categories: journal
tags: [Linux]
image: Jokulsarlon.jpg
---

# Settings

暫時生效
```shell
echo 0 | sudo tee /sys/module/hid_apple/parameters/fnmode
```

永久設定
```shell
echo "options hid_apple fnmode=0" | sudo tee /etc/modprobe.d/hid_apple.conf
sudo update-initramfs -c -k all  # To make sure it's loaded early enough.
```

# Reference
-- [Keychron Function Keys on Linux](https://stuvel.eu/post/2022-12-09-keychron-f-keys-on-linux/)
