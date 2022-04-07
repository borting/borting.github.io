---
layout: post
title: "List Branches Match a Patern and Show Author of Last Commit"
author: "Borting"
categories: journal
tags: [Git]
image: plum.jpg
---

看到一個蠻有卻的寫法, 可以列出所有 remote branch 的 last commit 的 author name 和 date
```shell
for branch in `git branch -r | grep -v HEAD`;do echo -e `git show --format="%ai by %an" $branch | head -n 1` \\t$branch; done
```

如果要看 last commit 的 committer 和 date, 則可以
```shell
for branch in `git branch -r | grep -v HEAD`;do echo -e `git show --format="%ci by can" $branch | head -n 1` \\t$branch; done
```
# Reference
* [List remote Git branches and the last commit's author and author date for each branch. Sort by most recent commit's author date](https://gist.github.com/l15n/3103708)
