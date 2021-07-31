---
layout: post
title: "Multipass: Ubuntu VMs on Demand"
author: "Borting"
categories: journal
tags: [Linux]
image: Jokulsarlon.jpg
---

Multipass 是 Canonical 打造的 hypervisor-based virtualization 工具.
目的是讓使用者在不同平台上 (Windows, Mac, and Linux) 都能快速建立 Ubuntu VMs.

和使用 virtualbox 等工具建立 VM 相比, Multipass 不需要花時間去安裝 OS, 可以直接從類似 DockerHub 的空間下載 image.
操作體驗和 docker 蠻像的.
此外, 抓下來的 image 接近 bare-bone Linux, 不包含任何 GUI packaga.
因此, 建一個 VM 的速度很快, 空間成本也低.

最近在玩 Jserv 的[練習題](https://hackmd.io/@sysprog/linux2021-summer/)時需要用到比較新的 kerenl, 但是又不想裝慢到炸的 virualbox.
所以裝了 Multipass 來玩玩看.

# Installation

On Linux
```
$ sudo snap install multipass --classic
```

# Basic Operations

* List available Ubuntu images
```
$ multipass find
```

* Create VMs
```shell
$ multipass launch [-c CPU_NUM] [-m MEM_SIZE] [-d DISK_SIZE] -n VM_NAME [IMAGE_NAME]

# Example: create a VM with 1G RAM, 5G disk space by default
$ multipass launch -n practice 20.04

# Example: create a VM with 4 cores, 2G RAM, 10G disk space
$ multipass launch -c 4 -m 2G -d 10G -n practice 20.04
```

* Start VM
```shell
$ multipass start VM_NAME
```

* Stop VM
```shell
$ multipass stop VM_NAME
```

* List available VMs
```shell
$ multipass list VM_NAME
```

* Check status of a VM
```shell
$ multipass info VM_NAME
```

* Login VM
```shell
$ multipass shell VM_NAME
```

* Exec command in a VM
```shell
$ multipass exec VM_NAME -- COMMAND
```

* Delete VM
```shell
$ multipass delete VM_NAME
$ multipass purge
```

# Reference

* [Multipass - 如 Docker 般的虛擬機](https://jackkuo.org/post/multipass_tutorial/)
* [Multipass Documentation](https://multipass.run/docs)
