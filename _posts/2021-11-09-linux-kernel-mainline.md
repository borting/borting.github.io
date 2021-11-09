---
layout: post
title: "Linux Kernel Mainline"
author: "Borting"
categories: journal
tags: [Linux,Git]
image: Jokulsarlon.jpg
---

Github 上的 [Kinux repos](https://github.com/torvalds/linux) 只算是 Linus 個人 repos 在 Github 上的 mirror.
真正的 repos (以及其他的 dev repos) 都 host 在 [git.kernel.org](https://git.kernel.org/) 上.

# Git Clone

* Clone mailine kernel repos (也就是 Linus 維護的 repos)
```shell
$ git clone git://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git
```

* Clone repos maintaining released branches
```shell
$ git clone git://git.kernel.org/pub/scm/linux/kernel/git/stable/linux.git

# Or add as another remote
$ git remote add stable git://git.kernel.org/pub/scm/linux/kernel/git/stable/linux.git
$ git fetch stable
```

# Reference
* [Why does the Linux kernel repository have only one branch?](https://stackoverflow.com/a/30268416)
