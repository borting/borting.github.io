---
layout: post
title: "Install Pipewire in Ubuntu 20.04"
author: "Borting"
categories: journal
tags: [Linux]
image: Jokulsarlon.jpg
---

把裝有 Ubuntu 20.04筆電的 wi-fi 網卡升級到 AX210 後, 藍芽耳機切換到 A2DP codec 後就沒有聲音了 QQ.
Google 了一下可能是 Ubuntu default 使用的 Pulseaudio 已經無法支援 AX210 的 BT driver.
只好參考網路的建議, 把 {ulseaudio 改成 Pipewire 試試看.

# Replace Pulseaudio with Pipewire

-- Add PPA
```shell
sudo add-apt-repository ppa:pipewire-debian/pipewire-upstream
sudo apt update
```

-- Install required package
```shell
sudo apt install pipewire libspa-0.2-bluetooth pipewire-audio-client-libraries
```

-- Reload the daemon, disable Pulseaudio, and enable Pipewire
```shell
systemctl --user daemon-reload
systemctl --user --now disable pulseaudio.service pulseaudio.socket
systemctl --user mask pulseaudio
systemctl --user --now enable pipewire-media-session.service
```

-- Reboot system and check Pipewire is connected
```shell
pactl info
```

# Referemce

* [Replacing Pulseaudio with Pipewire in Ubuntu 20.04](https://askubuntu.com/a/1339897)
