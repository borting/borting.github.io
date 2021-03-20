---
layout: post
title: "Install Git-Repo on Ubuntu 16.04"
author: "Borting"
categories: journal
tags: [Git,Repo,Python,Linux]
image: plum.jpg
---

工作上用 [`git-repo`](https://gerrit.googlesource.com/git-repo/+/refs/heads/master/README.md) 管理 SDK.
但在 Ubuntu 16.04 下透過 `apt install repo` 抓到的版本太舊, 一直要我升級.
升級後又噴 WARNING 說 python 版本不合.
紀錄一下在 Ubuntu 16.04 上安裝最新板 `git-repo` 的步驟.

# Install Required Packages

repo v2.8 需要搭配 python3.6 以上的版本

## Install python 3.6 from ppa

```bash
$ sudo apt-get install software-properties-common
$ sudo add-apt-repository ppa:deadsnakes/ppa
$ sudo apt-get update
$ sudo apt install python3.6 python3.6-dev python3.6-gdbm python3.6:amd64 python3.6-dev:amd64
```

## Set default python3 to python3.5

Ubuntu 16.04 的 Unity 界面是搭配 python3.5 寫的.
將預設 python 改成 python3.6 後, 許多 GUI widget 會因為缺少對應的 python3.6 library 報 WARNING 甚至無法開啟.
解決方式是把 default python3 改成 python3.5.
```bash
$ sudo update-alternatives --install /usr/bin/python3 python3 /usr/bin/python3.6 1
$ sudo update-alternatives --install /usr/bin/python3 python3 /usr/bin/python3.5 2
```

# Install Git-Repo

直接從 git-repo 的官網 clone 最新的版本 (2020.05 安裝時是 v2.8 板).
```bash
# Clone git-repo project
$ git clone https://gerrit.googlesource.com/git-repo
$ git co v2.8

# Copy repo to /usr/bin
$ sudo cp repo /usr/bin/repo
```

修改 `/usr/bin/repo` 第一行, 強制使用 python3.6 開啟.
```python
#!/usr/bin/env python3.6
```

## Git-Repo Configuration

在 `~/.repo_.gitconfig.json` 新增 `git-repo` 預設的 username 和 e-mail.
```json
{
  "user.name": [
    "Borting Chen"
  ],
  "user.email": [
    "bortingchen@gmail.com"
  ]
}
```

# Reference

* [How to change from default to alternative Python version on Debian Linux](https://linuxconfig.org/how-to-change-from-default-to-alternative-python-version-on-debian-linux)
