---
layout: post
title: "Speedup Repo Sync by Referencing Local Repository"
author: "Borting"
categories: journal
tags: [Repo, Git]
image: plum.jpg
---

在使用 [git-repo](https://gerrit.googlesource.com/git-repo/) 管理程式碼時, 如果 git project 很多或 git object store 很大 (e.g. linux kenrel), 在 `repo sync` 時間就會花上許多時間 fetch code.
一個加速的方法是利用 `git clone` 的 `reference` 功能, 參考 local 已 sync 過得 repo repository 裡的 git database:
* 先將 local repo repository 的 git object store 用 `git pack-objects` 將必要的 objects 打包傳到到新的 repo repository.
* 再透過 `git fetch` 將 remote repository 上新的 commits 抓下來

# Repo Sync with Reference

指令:
```shell
repo init -u MANIFEST_REPOS -m MANIFEST --reference=/PATH/TO/LOCAL/REPO/REPOSITORY --dissociate
repo sync
```

`--reference` 指定已在 local 下載好的 repo repository 位址.
`--dissociate` 代表 repo 在 git clone 完所有 projects 後, 不會再參考 local repo repository 的 git object store.
之後 `repo sync` 也會直接從 remote repository fetch.
這樣可以避免被 reference 的 local repo repository 被移除後, 後下載的 repo repository 的 git object store 損毀的狀況發生.

# Reference

* [How to dissociate linked objects in a live git repository (Or how to remove the internal reference from a shared mirror)](https://stackoverflow.com/q/61399458)
* [git-clone - Clone a repository into a new directory](https://git-scm.com/docs/git-clone#Documentation/git-clone.txt---reference-if-ableltrepositorygt)
