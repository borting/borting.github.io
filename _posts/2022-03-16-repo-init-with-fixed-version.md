---
layout: post
title: "Assign Repo Version Used for Repo-based Project Management"
author: "Borting"
categories: journal
tags: [Repo, Git]
image: plum.jpg
---

Google 的 [git-repo](https://gerrit.googlesource.com/git-repo/) 在 v2.19 後修改了每個 git project 的 `.git/` softlink 方式.
(參考 [commit 2a089cf](https://gerrit.googlesource.com/git-repo/+/2a089cfee4a3eb0c28cfb441861fc1fcb05797d3).)
這造成我用 docker 的 overlayfs 特性省 source code 空間的手法 (mount .repo 進 docker container) 失效.
所以目前的 workaround 方法就是指定 repo 的版本了.

# Repo Version

若不做設定, 每次 `repo init` 和 `repo sync` 都會從 [git-repo](https://gerrit.googlesource.com/git-repo/) fetch 最新的 code 下來, 並把最新的 tag merge 到 tag `stable`.

如果要指定 repo 的版本, 在 `repo init` 時要下額外參數 `--repo-rev` 指定下載版本
```shell
repo init -u MANIFEST_REPOS -m MANIFEST --repo-rev=v2.18
```

