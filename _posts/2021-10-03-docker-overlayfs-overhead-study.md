---
layout: post
title: "Docker OverlayFS Overhead Study"
author: "Borting"
categories: journal
tags: [Docker, container]
image: chimei-museum.jpg
---

利用 Docker 建立編譯環境已經成了近年來的顯學.
自己在工作上也會把編譯環境和 SDK 一起塞到 docker image 裡, 避免編譯不同 config 時要重新抓一包 SDK.
此外, 當 SDK 更新時, 也會基於舊的 SDK image 打上 patch 後建立一個新的 image, 減少新的 image 佔用的空間.
但基於 OverlayFS 的架構 (可以參考這篇有趣的[漫畫](https://jvns.ca/blog/2019/11/18/how-containers-work--overlayfs/)), 讀寫檔案時會從 upper layer 開始, 依序往 lower layer 找檔案.
因為編譯 SDK 會大量讀檔, 當 image 的 layer 數增加時, 會不會造成讀檔的速度變慢是一個值得探討的問題.

這邊先開一個 post 紀錄一下想做的實驗和參考資料.

# Git

* Count number of files in a git repository
```bash
$ git ls-files | wc -l
```

* Count changed .c/.h and Makefile
```bash
$ git diff --name-status commit_1 commit_2 | grep ".h$\|.c$\|Makefile" | wc -l
```

# Tools

## Docker Image/Layer Content Explorer

* [dive](https://github.com/wagoodman/dive)

## Linux Kernel Compile Time Measurement

* [Phoronix Test Suite](https://github.com/phoronix-test-suite/phoronix-test-suite/)
* [hyperfine](https://github.com/sharkdp/hyperfine)

# Reference

* [How containers work: overlayfs](https://jvns.ca/blog/2019/11/18/how-containers-work--overlayfs/)
* [About storage drivers](https://docs.docker.com/storage/storagedriver/)
* [Docker storage drivers](https://docs.docker.com/storage/storagedriver/select-storage-driver/)
* [Use the ZFS storage driver](https://docs.docker.com/storage/storagedriver/zfs-driver/)
* [Use the OverlayFS storage driver](https://docs.docker.com/storage/storagedriver/overlayfs-driver/)
* [How to Compile a Linux Kernel](https://www.linux.com/topic/desktop/how-compile-linux-kernel-0/)
* [Timed Linux Kernel Compilation](https://openbenchmarking.org/test/pts/build-linux-kernel)
* [Measuring Kernel Compile Times with Clang](https://linuxplumbersconf.org/event/7/contributions/802/attachments/652/1192/Measuring_Kernel_Compile_Times_w__Clang.pdf)
