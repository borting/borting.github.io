---
layout: post
title: "Push Part of Commits to Remote"
author: "Borting"
categories: journal
tags: [Git]
image: plum.jpg
---

紀錄一下 push 部份 commit(s) 到 remote 的方法.
以免再發生因為沒有把未完成的文章 `git add` 到 staging area 而被 `git clean` 誤刪的憾事 QQ.

# Git Push

通常我們要 push commits 到 remote 時, 我們會用以下指令將目前 branch 的 tip push 到 remote.
```bash
$ git push origin branch_name
```

實際上, 這個指令應該展開成以下格式.
當沒有寫 remote branch name 時, 則把 local branch name 作為 remote branch name.
```bash
$ git push origin local_branch_name[:retmote_branch_name]
```

下一步, git 把 local branch name 視為 commit-ish object, 抓出 branch tip 指向的 commit, 再將該 commit (以及 parent commits) push 到 remote.
所以, 指令可以進一步展開成
```bash
$ git push origin commit_of_local_branch_tip:remote_branch_name
```

到這裡, 我們就可以知道要怎 push 部份 commit(s) 到 remote 啦.
只要寫上要 push 的最後的 commit 的 SHA-1 ID 就可以了.
```bash
$ git push origin last_commit_to_psuh:remote_branch_name
```

# Video Resource

也可以高見龍老師的影片教學喔
<iframe width="560" height="315" src="https://www.youtube.com/embed/VShhhq_5sMc" title="如何使用 Git Push 指令只 Push 部份的進度？" frameborder="0" allowfullscreen></iframe>

# Reference

- [git push: Push all commits except the last one](https://stackoverflow.com/a/49954632)
