---
layout: post
title: "Set Upstream while Push Git Branch to Remote"
author: "Borting"
categories: journal
tags: [Git]
image: plum.jpg
---

把 local 的 branch push 到 remote 後, 同常也會同步設定將 local 的 branch 的 upstream 設成 remote branch.
這樣之後 fetch/pull 時就會自動更新.
以下紀錄作法.

# Set Upstream

* 若 local branch 已經 push 到 remote, 可以額外下指令設定 upstream.
```shell
git push REMOTE_NAME LOCAL_BRANCH_NAME
git branch --set-upstream-to=REMOTE_NAME/REMOTE_BRANCH_NAME LOCAL_BRANCH_NAME
```

* 也可以在第一次 push 時直接設定 upstream
```shell
git push -u REMOTE_NAME LOCAL_BRANCH_NAME[:REMOTE_BRANCH_NAME]
```

# Set Merge Rule on a Specific Branch

* 當從 remote pull 下來時, 可以設定 branch 只能走 fast-forward merge
```shell
git config branch.BRANCH_NAME.mergeOptions --ff-only
```
如此, 在 .git/config 可以看到
```
[branch "BRANCH_NAME"]
    mergeOptions = --ff-only
```

# Set Global Pull Rule

* Use rebase as default pull merge rule
```shell
git config --global pull.rebase true
```

# Ignore Merging Commit and 

* Git default pull.rebase 為 false, 此時 pull 後會採用 merge 方式, 後 local 的 commit 產生一個新的 merge commit
* 如果 git pull 後, 產生一個新的 merge commmit, 但此時 remote 又有新的 commit 時, 此時 push 會失敗.
解決方法
```shell
git fetch origin
git rebase −p REMOTE_NANE/REMOTE_BRANCH_NAME
```

# Reference
* [How do I push a new local branch to a remote Git repository and track it too?](https://stackoverflow.com/a/6232535)
* [Enforce fast forward as merge strategy in Git](https://code-maven.com/enforce-fast-forward-as-merge-strategy)
* [Git - When to Merge vs. When to Rebase](https://www.derekgourlay.com/blog/git-when-to-merge-vs-when-to-rebase/)
