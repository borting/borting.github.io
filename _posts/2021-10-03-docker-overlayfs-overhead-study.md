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
$ git ls-files | grep ".dts$\|.dtsi$\|.h$\|.c$\|Makefile" | wc -l
```

* Count changed .c/.h, .dts/.dtsi, and Makefile
```bash
$ git diff --name-status commit_1 commit_2 | grep ".dts$\|.dtsi$\|.h$\|.c$\|Makefile" | wc -l
```

# Kernel Build Environemnt

```shell
$ sudo apt update
$ sudo apt install git fakeroot build-essential ncurses-dev xz-utils libssl-dev bc flex libelf-dev bison cpio lz4
$ sudo apt install vim wget 
```

# Kernel Build Command

* Copy linux config
```shell
$ cp /boot/config-$(uname -r) .config
```

* Test command
```shell
$ make mrproper && git clean -f && git reset --hard HEAD

$ git co v5.10

$ make mrproper && git clean -f && git reset --hard HEAD && cp ../linux_config .config && yes "x" | make menuconfig
$ make -j $(nproc) &> make.log
```

* Run with hyperfine
```shell
$ make mrproper && git clean -f && git reset --hard HEAD

$ git co v5.10

$ sudo sync; echo 3 | sudo tee /proc/sys/vm/drop_caches
$ hyperfine -r10 'make mrproper && git clean -f && git reset --hard HEAD && cp ../linux_config .config && yes "x" | make menuconfig && make -j $(nproc) --silent'
```

# Hyperfine

## Installation
* Download latest release from [github](https://github.com/sharkdp/hyperfine/releases/)
* Install using dpkg
```shell
$ sudo dpkg -i hyperfine_1.12.0_amd64.deb
```

## Usage

* Basic executaion
```shell
$ hyperfine 'command'

# Example
$ hyperfine 'sleep 1'
```

* Set number of runs
```shell
# Run 5 times
$ hyperfine -r5 'sleep 1'
```

* Run benchmark on a warn cache, i.e. doing server pre-run
```shell
# Do 3 pre-run
$ hyperfine -w 3 'sleep 1'
```

* Run benchmark on a cold cache
```shell
$ hyperfine --prepare 'sync; echo 3 | sudo tee /proc/sys/vm/drop_caches' 'sleep 1'
```

* Final command
```shell
$ make mrproper && git clean -f && git reset --hard HEAD
$ sudo sync; echo 3 | sudo tee /proc/sys/vm/drop_caches
$ hyperfine -r3 'make mrproper && git clean -f && git reset --hard HEAD && cp /boot/config-$(uname -r) .config && yes "x" | make menuconfig && make -j $(nproc) --silent'

# hyperfine -r3 'make mrproper && git clean -f && git reset --hard HEAD && cp ../linux_config .config && yes "x" | make menuconfig && make -j $(nproc) --silent'
```

Note: set 
```shell
CONFIG_SYSTEM_TRUSTED_KEYS=""
CONFIG_SYSTEM_REVOCATION_KEYS=""
```

# Linux Source for Docker

* Checkout to the base commit
```shell
$ git co v5.10
```

* Move .git/ to somewhere out of the repos folder
```shell
$ mv .git ../linux_git
```

* Copy part of files in .git
```shell
$ mkdir .git
$ cp ../linux_git/HEAD .git/
$ cp ../linux_git/index .git/
```

* Pack the git repos, restore .git

* Unpack the archive and change the owner to root

# Update Linux source in Docker

* Folders need to mount from outside
```
.git/objects
.git/refs

# Maybe not
.git/packed-refs

# Maybe unnecessary
.git/config
.git/description
.git/hooks
.git/info
.git/logs

# Ignore if outside SDK do not have this
.git/branches
.git/rr-cache
.git/svn
```

* Update repos inside docker
```shell
# Checkout tag or commit ID
# DONOT checkout branch since this creates new entry in .git/config
$ git checkout <commit-ish>
```

* Reset HEAD's ownership under .git after update from docker
```shell
$ find .git/ -name "HEAD" | xargs chown ${UID}:${UID}
```

* Execute container
```shell
$ docker run --rm -it -v ${HOME}/repos/linux/.git/objects:/root/linux/.git/objects:ro \
  -v ${HOME}/repos/linux/.git/refs:/root/linux/.git/refs:ro \
  -v ${HOME}/repos/linux/.git/packed-refs:/root/linux/.git/packed-refs:ro \
  docker_image
```

# Test

* test 5.10 ~ 5.10.78: layer size, layer-by-layer compile time, number of file change
* test 5.10, 5.11, 5.12, 5.13: layer size, layer-by-layer compile time, number of file change
* test 5.4 ~ 5.4.158: layer size, number of file change

# Tools

## Docker Image/Layer Content Explorer

* [dive](https://github.com/wagoodman/dive)

## Linux Kernel Compile Time Measurement

* [Phoronix Test Suite](https://github.com/phoronix-test-suite/phoronix-test-suite/)
* [hyperfine](https://github.com/sharkdp/hyperfine)
* [Linux kernel compile benchmarks kcbench & kcbenchrate](https://gitlab.com/knurd42/kcbench/-/tree/master) ([intro](http://thorstenl.blogspot.com/2020/06/kcbench-linux-kernel-compile-benchmark.html))
* [tuxmake](https://gitlab.com/Linaro/tuxmake) ([intro](https://lwn.net/Articles/841624/))

##  Git Timestamp

* [Git Tools](https://github.com/MestreLion/git-tools)

# Reference

* [How containers work: overlayfs](https://jvns.ca/blog/2019/11/18/how-containers-work--overlayfs/)
* [About storage drivers](https://docs.docker.com/storage/storagedriver/)
* [Docker storage drivers](https://docs.docker.com/storage/storagedriver/select-storage-driver/)
* [Use the ZFS storage driver](https://docs.docker.com/storage/storagedriver/zfs-driver/)
* [Use the OverlayFS storage driver](https://docs.docker.com/storage/storagedriver/overlayfs-driver/)
* [How to Compile a Linux Kernel](https://www.linux.com/topic/desktop/how-compile-linux-kernel-0/)
* [Timed Linux Kernel Compilation](https://openbenchmarking.org/test/pts/build-linux-kernel)
* [Measuring Kernel Compile Times with Clang](https://linuxplumbersconf.org/event/7/contributions/802/attachments/652/1192/Measuring_Kernel_Compile_Times_w__Clang.pdf)
* [kcbench, the Linux kernel compile benchmark, version 0.9.0 is out](http://thorstenl.blogspot.com/2020/06/kcbench-linux-kernel-compile-benchmark.html)
* [How to determine the maximum number to pass to make -j option?](https://unix.stackexchange.com/a/208569)
* [Compiling the kernel 5.11.11](https://askubuntu.com/a/1329625)
* [Portable and reproducible kernel builds with TuxMake](https://lwn.net/Articles/841624/)
* [談談.git 目錄](http://wen00072.github.io/blog/2015/03/10/talk-git-directory/)
