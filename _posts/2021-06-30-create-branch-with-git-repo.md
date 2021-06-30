---
layout: post
title: "Create Branches with Git-Repo"
author: "Borting"
categories: journal
tags: [Git,Repo]
image: plum.jpg
---

[Git-Repo](https://gerrit.googlesource.com/git-repo/+/refs/heads/master/README.md) 可以用來同時管理多個 git repository, 適合用在大型專案, e.g. SDK 的開發上.
通常, 我只要在我負責的 component 的 git repository 上 commoit code 就好.
但在整合或 debug 時, 還是有機會動到別的 git repositories.
要在每個每個 git repositories 都建 branch 實在太麻煩了, 還好 Git-Repo 有提供快速指令

# Create Branches on all Repository

```bash
$ cd /path/to/root/of/git/repo
$ repo start BRANCH_NAME --all
```

# Reference

* [Creating local branches with Repo](https://stackoverflow.com/q/33777359)
* [Version Control with Repo and Git](https://android.googlesource.com/platform/docs/source.android.com/+/ics-mr0/src/source/version-control.md)
