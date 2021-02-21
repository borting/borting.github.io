---
layout: post
title: "Checkout GitHub Pull Request"
author: "Borting"
categories: journal
tags: [Git, GitHub]
image: plum.jpg
---

Gerrit 用久了, 就很習慣下 `git review -d CHANGE_ID` 抓 code 下來 review.
最近剛好有人送了一個 pull request 到我的 GitHub 上, 就想試試看 GitHub 是否有類似的作法.

# GitHub Pull Request

GitHub 的 pull request 是放在 `refs/pull/*`.

## Checkout A Single Pull Request

若只抓抓單一 pull request 下來 review.
```bash
$ git fetch origin pull/PR_ID/head:BRANCHNAME
$ git checkout BRANCHNAME

# e.g.
$ git fetch origin pull/666/head:bug-fix
$ git checkout bug-fix
```

也可以在 `~/.gitconfig` 新增一個 git alias 把兩個指令合成一個
```gitconfig
[alias]
    pr = "!f() { if [ -z "$2" ]; then git fetch origin pull/${1}/head:pr/${1} && git checkout pr/${1}; else git fetch origin pull/$1/head:$2 && git checkout $2; fi; }; f"
```
使用方式
```bash
$ git pr 666          # Checkout to pr/666 branch
$ git pr 666 bug-666  # Checlout to bug-666 branch
```

## Fetch All Pull Requests

若要在 `git fetch` 或是 `git pull` 時把所有的 pull requests 抓下來, 則要在 repos 的 `.git/config` 做以下設定.
```gitconfig
[remote "origin"]
    fetch = +refs/pull/*/head:refs/remotes/origin/pr/*
```

## GitHub Cli

另一方法是用 [GitHub Cli](https://cli.github.com/manual/gh_pr), 來做.
等有時間再來玩囉.

# Reference

* [Checking out pull requests locally](https://docs.github.com/en/github/collaborating-with-issues-and-pull-requests/checking-out-pull-requests-locally)
* [Checkout github pull requests locally](https://gist.github.com/piscisaureus/3342247)
* [GitHub Cli -- gh pr](https://cli.github.com/manual/gh_pr)
