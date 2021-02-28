---
layout: post
title: "Fake Kernel Version inside Container"
author: "Borting"
categories: journal
tags: [container,Docker,Singularity]
image: chimei-museum.jpg
---

工作上常用 Docker container 編譯 SDK.
最近發現只要 host 作業系統一更新, SDK 裡部份 libs/apps 在編譯過程就會報 `Ivalid kernel source directory ...` 錯誤.

錯誤的原因是這些 libs/apps 的 code 有 include kernel header, 因此在 `./configure` 過程中要透過 `uname -r` 去取得 kernel version, 組合出 kernel source 路徑.
但因 [container 和 host 共用 kernel](https://en.wikipedia.org/wiki/OS-level_virtualization), 且 `uname -r` 透過 `uname()` system call 取得 kernel version, 在 container 裡執行 `uname -r` 實際上拿到的是 host 
的 kernel version.
當系統更新導致 host 的 kernel version 和 Docker image 在建立時的 kernel version 不一致時, `./configure` 就會找不到正確的 kernel source 路徑.

# Fakeuname

解決此問題最簡單的方法就是在呼叫 `uname -r` 時, 攔截這個指令. 並丟 Docker/Singularity image 建立時的 kernel version 回去.
所以我寫了一個 **[fakeuname](https://github.com/borting/fakeuname)** project 來瞞天過海 (XD).
概念如下:
* 修改 PATH env 讓呼叫 `uname` 時執行一個假造的 `uname` scipt
* 在假造的 `uname` script 回傳 image 建立時的 kernel version

## 建立假造的 uname script

```bash
$ mkdir ~/.local/fakeuname
$ vim ~/.local/fakeuname/uname
$ chmod u+x ~/.local/fakeuname/uname
```

以下是一個簡單的 `uname` script 範例:
```bash
#!/usr/bin/env bash

# Set the kernel release string you attempt to present here.
FAKE_KERNEL_RELEASE="4.15.0-112-generic"

# Reset in case getopts has been used previously in the shell.
OPTIND=1

# Substitute kernel release string
OUTPUT=`/bin/uname $@`
while getopts "asnrvmpio" opt; do
    case "${opt}" in
        r)
            if [[ ! -z ${FAKE_KERNEL_RELEASE+x} ]]; then
                ORG_KERNEL_RELEASE=`/bin/uname -r`
                OUTPUT=${OUTPUT/${ORG_KERNEL_RELEASE}/${FAKE_KERNEL_RELEASE}}
            fi
            ;;
        *)
            ;;
    esac
done
echo ${OUTPUT}
```

## 修改 PATH env

在 `~/.bashrc` 透過新增 PATH env 來修改 command search 的順序, 讓我們假造的 `uname` script 先被執行.
此外, 為了不影響在 host 環境下的 `uanme` 操作, PATH env 應該在 container 環境下才被 updated.
```bash
# Update PATH env if we are in a Docker container or Singularity container
if test -f "/.dockerenv" || [[ ! -z "${SINGULARITY_NAME+x}" ]]; then
    export PATH=${HOME}/.local/fakeuname:${PATH}
fi
```

## 執行 Container 時新增的指令

執行 Docker container 時, 需要額外 mount `~/.bashrc` 和 `~/.local/fakeuname`.
```bash
$ docker run -v ${HOME}/.bashrc:/root/.bashrc:ro -v ${HOME}/.local/fakeuname/uname:/root/.local/fakeuname/uname --rm -it DOCKER_IMAGE
```

執行 Singularity container 時預設會 mount 家目錄, 所以不需額外指令.
```bash
$ SINGULARITY_SHELL=/bin/bash singularity shell SINGULARITY_IMAGE
```

## 其他作法

另一個假造 kernel version 的作法是透過 `LD_PRELOAD` 去 override `uname()` system call.
詳細作法可以參考 [rpm-software-management/fakeuname](https://github.com/rpm-software-management/fakeuname).

但使用 `LD_PRELOAD` 的方法在 cross compiling 時會因為 host 和 target ELF 格式不同, 導致在 load `LD_PRELOAD` 的 `.so` 檔時報錯.
```bash
ERROR: ld.so: object '/home/borting/.local/fakeuname/fakeuname.so' from LD_PRELOAD cannot be preloaded (wrong ELF class: ELFCLASS64): ignored.
```

雖然 `LD_PRELOAD` 的 `.so` 檔最終不會編進 binary, 但在 make log 看到噴 error 就不大爽, 所以最後就不採用這種方法了.

# Reference

* [brenkem/fake-uname](https://github.com/brenkem/fake-uname/blob/master/uname)
* [How to spoof uname -rs per process](https://unix.stackexchange.com/a/270099)
* [rpm-software-management/fakeuname](https://github.com/rpm-software-management/fakeuname)
* [What Is the LD_PRELOAD Trick?](https://www.baeldung.com/linux/ld_preload-trick-what-is)

