---
layout: post
title: "Sync Deleted Git Branches between Local and Remote"
author: "Borting"
categories: journal
tags: [Git]
image: cards.jpg
---

紀錄 local 和 remote 如何同步刪除 git branch

# Delete Remote Branches From Local

* 刪除 remote branch 前不需要先刪除 local branch
```shell
git push REMOTE_NAME --delete BRANCH_NAME
```

# Sync Branches Deleted at Remote

* Git 預設在 local pull/fetch 時, 不會自動刪除 remote 已刪除的 branch.
需要下指令:
```shell
git remote prune REMOTE_NAME
```
或是在 `.git/config` 設定:
```shell
git config remote.BRANCH_NAME.prune true
```

* 若刪除的 remote branch 已在 local checkout, 則 local branch 仍需手動刪除

# Reference
* [How to delete remote branches in Git](https://www.educative.io/edpresso/how-to-delete-remote-branches-in-git)
* [When does Git refresh the list of remote branches?](https://stackoverflow.com/a/36358502)
