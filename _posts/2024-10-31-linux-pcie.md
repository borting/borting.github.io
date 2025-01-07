---
layout: post
title: "Linux PCI Device"
author: "Borting"
categories: journal
tags: [Linux]
image: Jokulsarlon.jpg
---

# PCI device and Sysfs

List all PCI devices
```shell
lspci

# Example
00:00.0 Host bridge: Intel Corporation Xeon E3-1200 v5/E3-1500 v5/6th Gen Core Processor Host Bridge/DRAM Registers (rev 08)
00:02.0 VGA compatible controller: Intel Corporation Skylake GT2 [HD Graphics 520] (rev 07)
00:04.0 Signal processing controller: Intel Corporation Xeon E3-1200 v5/E3-1500 v5/6th Gen Core Processor Thermal Subsystem (rev 08)
00:14.0 USB controller: Intel Corporation Sunrise Point-LP USB 3.0 xHCI Controller (rev 21)
00:14.2 Signal processing controller: Intel Corporation Sunrise Point-LP Thermal subsystem (rev 21)
00:16.0 Communication controller: Intel Corporation Sunrise Point-LP CSME HECI #1 (rev 21)
00:17.0 SATA controller: Intel Corporation Sunrise Point-LP SATA Controller [AHCI mode] (rev 21)
00:1c.0 PCI bridge: Intel Corporation Sunrise Point-LP PCI Express Root Port #6 (rev f1)
00:1c.7 PCI bridge: Intel Corporation Sunrise Point-LP PCI Express Root Port #8 (rev f1)
00:1f.0 ISA bridge: Intel Corporation Sunrise Point-LP LPC Controller (rev 21)
00:1f.2 Memory controller: Intel Corporation Sunrise Point-LP PMC (rev 21)
00:1f.3 Audio device: Intel Corporation Sunrise Point-LP HD Audio (rev 21)
00:1f.4 SMBus: Intel Corporation Sunrise Point-LP SMBus (rev 21)
00:1f.6 Ethernet controller: Intel Corporation Ethernet Connection I219-V (rev 21)
01:00.0 Network controller: Intel Corporation Device 2725 (rev 1a)
02:00.0 Unassigned class [ff00]: Alcor Micro AU6621 PCI-E Flash card reader controller
```

在 sysfs 可以找到 access PCI resource 的路徑
```shell
ls /sys/bus/pci/devices/

# Example
0000:00:00.0  0000:00:04.0  0000:00:14.2  0000:00:17.0  0000:00:1c.7  0000:00:1f.2  0000:00:1f.4  0000:01:00.0
0000:00:02.0  0000:00:14.0  0000:00:16.0  0000:00:1c.0  0000:00:1f.0  0000:00:1f.3  0000:00:1f.6  0000:02:00.0
```

# PCI Configuration Space

Dump PCI configuration space, 第一個 word 和 第二個 word 分別是 vendor ID 和 device ID
```shell
lspci -s 00:1f.6 -xxxx
```

# Hotplug PCI

若支援 hot-plug, 在重新插拔後, 要 rescan PCI 才能找到新的 device
```shell
# Remove
sudo bash -c "echo 1 > /sys/bus/pci/devices/0000\:00\:1f.6/remove"

# Rescan
sudo bash -c "echo 1 > /sys/bus/pci/rescan"
```

# Reference
* [Accessing PCI device resources through sysfs](https://docs.kernel.org/PCI/sysfs-pci.html)
