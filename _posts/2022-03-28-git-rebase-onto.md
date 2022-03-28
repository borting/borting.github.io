---
layout: post
title: "Git Rebase Onto"
author: "Borting"
categories: journal
tags: [Git]
image: plu.jpg
---

`Git rebase --onto` 指令可把某一段連續的 commits rebase 到新的 parent commit 上.
我通常會用這個方式捨棄當前 branch 和目標 branch 重複但不完全相同的 commits, 避免直接 rebase 時遇到 conflict.

# Basic Git Rebase

* Rebase current HEAD to NEW\_PARENT\_COMMIT
```shell
git rebase NEW_PARENT_COMMIT
```

* Rebase BRANCH on NEW\_PARENT\_COMMIT, then switch to new BRANCH
```shell
git rebase NEW_PARENT_COMMIT BRANCH
```

# Rebase a Series of Commits by --onto

* 所有要被 rebase 的 commits 都要是當下 HEAD reachable 的 commit, 也就是 HEAD 的 parent.
所以, 要先 checkout 要被 rebase commits 所在的 branch
```shell
git checkout BRANCH_CONTAINS_COMMITS_TO_BE_REBASED
```
或是 checkout 要被 rebase 的連續 commits 的最後一個 commit
```shell
git checkout LAST_COMMIT_OF_COMMITS_TO_BE_REBASED
```

* 把 HEAD 上的某個 commit (REACHABLE\_COMMIT) 的 parent comnmit (PARENT\_OF\_REACHABLE\_COMMIT) 替換成另一個 commit (NEW\_PARENT\_COMMIT)
```shell
git rebase --onto NEW_PARENT_COMMIT PARENT_OF_REACHABLE_COMMIT
```

* 把 HEAD 上的一段連續的 commits (FIRST\_OF\_REACHABLE\_COMMITS 到 LAST\_OF\_REACHABLE\_COMMITS) 取出, 並把這段連續 commits 的 parent 替換成另一個 commit (NEW\_PARENT\_COMMIT).
操作完後, HEAD 會指向新的 LAST\_OF\_REACHABLE\_COMMITS.
```shell
git rebase --onto NEW_PARENT_COMMIT PARENT_OF_FIRST_OF_REACHABLE_COMMITS LAST_OF_REACHABLE_COMMITS
```
 
* 若 LAST\_OF\_REACHABLE\_COMMITS 改成 BRANCH\_NAME, 則 LAST\_OF\_REACHABLE\_COMMITS 即是 BRANCH\_NAME 的 tips.
操作完後, HEAD 和 BRANCH\_NAME 的 tips 都會指向 LAST\_OF\_REACHABLE\_COMMITS
```shell
git rebase --onto NEW_PARENT_COMMIT PARENT_OF_FIRST_OF_REACHABLE_COMMITS BRANCH_NAME
```
效果等同如下指令.
```shell
git checkout BRANCH_NAME
git rebase --onto NEW_PARENT_COMMIT PARENT_OF_FIRST_OF_REACHABLE_COMMITS
```

# Concliusion

Syntax
```shell
# Rebase a series of commits
git checkout BRANCH_NAME or REBASE_UNTIL_COMMI
git rebase --onto NEW_PARENT_COMMIT OLD_PARRENT_COMMIT <REBASE_UNTIL_COMMIT>

# Rebase a part of commits of a branch
git checkout BRANCH_NAME
git rebase --onto NEW_PARENT_COMMIT OLD_PARRENT_COMMIT
```

# Reference
* [Git rebase --onto an overview](https://womanonrails.com/git-rebase-onto)
* [Git Doc -- git-rebase](https://git-scm.com/docs/git-rebase)
