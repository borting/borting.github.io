---
layout: post
title: "Use Git Fetch instead of Git Pull"
author: "Borting"
categories: journal
tags: [Git]
image: plum.jpg
---

紀錄一下使用 `git fetch` 來更新 branch 的方法, 取代平常使用的 `git pull`

# Use High-level Commands

常見的作法是先 `git fetch`, 然後 `git merge` upstream
```shell
git fetch origin
git checkout BRANCH_NAME
git merge BRANCH_NAME refs/remotes/origin/UPSTREAM_NAME
```

若不想產生 merge commit, 也可以使用 `git pull`, 但已在 local commit 的 commits 會被 rebase
```shell
git fetch origin
git checkout BRANCH_NAME
git rebase refs/remotes/origin/UPSTREAM_NAME
```

若不想讓 local commits 被 rebase, 可以使用 `git cherry-pick`
```shell
git fetch origin
git checkout BRANCH_NAME
git cherry-pick refs/remotes/origin/UPSTREAM_NAME
```

# Use Low-level Commands

直接 `git update-ref`
```shell
git fetch origin
git update-ref BRANCH_NAME refs/remotes/origin/UPSTREAM_NAME
```

或是直接反查 upstream
```shell
git fetch origin
git update-ref BRANCH_NAME refs/remotes/`git rev-parse --abbrev-ref BRANCH_NAME@{u}`
```

# Reference
* [Git: 四種將分支與主線同步的方法](https://www.cythilya.tw/2018/06/19/git-merge-branch-into-master/)
* [What is the difference between 'git pull' and 'git fetch'?](https://stackoverflow.com/a/292359)
* [Can "git pull --all" update all my local branches?](https://stackoverflow.com/a/624443)
* [git: fetch and merge, don’t pull](https://longair.net/blog/2009/04/16/git-fetch-and-merge/)
* [understanding git fetch then merge](https://stackoverflow.com/a/3427698)
