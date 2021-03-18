---
layout: post
title: "Add SSH key to GitHub Account"
author: "Borting"
categories: journal
tags: [GitHub]
image: plum.jpg
---

紀錄在 GitHub 上新增 SSH key 的方法, 省去 clone 和 push 時打密碼的麻煩.

# Local Host Configutation

建立 SSH keys
```bash
# Create rsa key with 4096 bits
$ ssh-keygen -t rsa -b 4096
```

在 `~/.ssh/config` 新增 GitHub 連線資訊
```bash
Host github
    HostName github.com
    User git
    IdentityFile ~/.ssh/id_rsa
```

# GitHub Configuration

基本上參考[官網的說明](https://docs.github.com/en/github/authenticating-to-github/adding-a-new-ssh-key-to-your-github-account)就可以了
* Click head icon on GitHub website --> "Settings" --> "SSH and GPG keys" --> Click "New SSH key".
* Paste content of public key (`~/.ssh/id_rsa.pub`) to "Key" field.

# Cline Repos

設定好後 clone / push 就不需要打密碼了.
也可以直接 clone GitHub 提供的 SSH link.
```bash
$ git clone git@github.com:borting/borting.github.io.git
```

# Reference

* [Adding a new SSH key to your GitHub account](https://docs.github.com/en/github/authenticating-to-github/adding-a-new-ssh-key-to-your-github-account)
* [Git 版本控制筆記 - 使用 github 及 ssh 金鑰設定](https://blog.jaycetyle.com/2018/02/github-ssh/)
* [Git 踩坑紀錄（二）git clone with SSH keys 或 HTTPS 設定步驟](https://medium.com/@tsengbatty/bdb721bd7cf2)
