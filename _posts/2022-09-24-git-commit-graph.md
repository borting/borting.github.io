---
layout: post
title: "Git Commit Graph for Faster Visualized Log Display"
author: "Borting"
categories: journal
tags: [Git]
image: plum.jpg
---

在開大型 git project (e.g. linux) 的 log 時, 常會花上幾秒鐘的時間讀取.
原因是 git 需要重複做以下動作: 讀取 commit 的 content --> 找到 parent commit --> 讀取 parent commit 的 content --> (loop).
為了加速 log 的讀取, git 提供了 `git commit-graph` 指令來建立一個簡化 parrent commit 讀取速度的簡易 graph database.

# Commit Graph

* Generate the new commit graph by walking commits starting at all refs
```shell
git commit-graph write --reachable
```

* Write a commit-graph file containing all reachable commits.
```shell
git show-ref -s | git commit-graph write --stdin-commits
```

* Generate new commit graph while including all commits that are present in the existing commit-graph file
```shell
git rev-parse HEAD | git commit-graph write --stdin-commits --append
```

生成的 commit graph 會放在 `.git/objects/info/commit-graph`.
Commit graph 裡包含 object ID, parent relationships, commit date and root tree information.

# Automatic Graph Update

Method 1:
```shell
git config fetch.writeCommitGraph=true
```

Method 2:
```shell
git maintenance start
```


# Reference
* [Git’s database internals II: commit history queries](https://github.blog/2022-08-30-gits-database-internals-ii-commit-history-queries/)
* [git-commit-graph](https://git-scm.com/docs/git-commit-graph)
