---
layout: post
title: "Use Git Fetch instead of Git Pull"
author: "Borting"
categories: journal
tags: [Git]
image: plum.jpg
---

紀錄一下使用 `git fetch` 來更新 branch 的方法, 取代平常使用的 `git pull`

# 方法

常見的作法是先 `git fetch`, 然後 `git merge` upstream
```shell
git fetch origin
git checkout BRANCH_NAME
git update-ref BRANCH_NAME refs/remotes/UPSTREAM_NAME
```

```shell
git fetch origin
git update-ref BRANCH_NAME refs/remotes/UPSTREAM_NAME
```

或是直接反查 upstream
```shell
git fetch origin
git update-ref BRANCH_NAME refs/remotes/`git rev-parse --abbrev-ref BRANCH_NAME@{u}`
```

# Reference

* [What is the difference between 'git pull' and 'git fetch'?](https://stackoverflow.com/a/292359)
* [Can "git pull --all" update all my local branches?](https://stackoverflow.com/a/624443)
* [git: fetch and merge, don’t pull](https://longair.net/blog/2009/04/16/git-fetch-and-merge/)
* [understanding git fetch then merge](https://stackoverflow.com/a/3427698)
