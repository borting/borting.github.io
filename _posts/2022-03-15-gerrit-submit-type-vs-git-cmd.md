---
layout: post
title: "Gerrit Submit Types vs. Git Commands"
author: "Borting"
categories: journal
tags: [Git, Gerrit]
image: plum.jpg
---

使用 Gerrit 管理 git projects 時, 使用者的 commit 會先 push 到一個 review branch, 形成一個 Gerrit change.
等 review 和 CI 流程跑完 (reviewed+2/verified+1), 使用者從 Gerrit submit change 後, commit 才會正式 submit 到 git repository 中.
(注意: 雖然使用的動詞都是 `submit`, 但 Gerrit 管理的是 changes, git 管理的是 commits.)
Gerrit 提供了數種 submit (change) types 來操作 commits 合回 git repository 的方式.
以下用表格比較與說明 Gerrit submit types 對應的 git commands 操作.

# Gerrit Submit Types vs Git Commands

Gerrit Submit Types | Git Commands
--------------------|--------------------------------
Fast Forward Only   | git merge --ff-only REVIEW\_BRANCH
Merge If Necessary  | git merge --ff REVIEW\_BRANCH
Always Merge        | git merge --no-ff REVIEW\_BRANCH
Cherry Pick         | git cherry-pick REVIEW\_BRANCH
Rebase If Necessary | git rebase REVIEW\_BRANCH
Rebase Always       | git rebase --no-ff REVIEW\_BRANCH (???)

* `Fast Forward Only` 只有 commit 的 parent 為 target branch 的 tip 才可以 submit, 否則要 rebase.
最嚴格的 submit type
* `Always Merge` 一定會產生一個 merge commit
* `Rebase Always` 一定會 rebase 且產生一個新的 commit.
目前還找不到對應的 command, 不確定是否是 `git rebase --no-ff` 有 bug, 還是如果 Gerrit 判定可以 fast fowarding 的話就改用 cherry-pick 的方式產生一個新的 commit.

# Reference
* [Gerrit Document -- Submit Type](https://gerrit-documentation.storage.googleapis.com/Documentation/3.5.0.1/config-project-config.html#submit-type)
* [Understanding and Applying Gerrit, Part 3: Gerrit Submit Types and Git-Review](https://nofluffjuststuff.com/magazine/2016/04/understanding_and_applying_gerrit_part_3_gerrit_submit_types_and_git_review)
* [何時該用 git merge --no-ff？](https://medium.com/@fcamel/d765c3a6bef5a)
* [What effect does the '--no-ff' flag have for 'git merge'?](https://stackoverflow.com/questions/9069061/what-effect-does-the-no-ff-flag-have-for-git-merge)
