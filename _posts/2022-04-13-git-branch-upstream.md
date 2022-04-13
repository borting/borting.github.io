---
layout: post
title: "List Upstreams of Git Branches"
author: "Borting"
categories: journal
tags: [Git]
image: plum.jpg
---

有時候 git remote 設太多, 要查詢 branch tracking 的 upstream 可以用以下方式.

* List upstreams of all branches
```shell
git branch -vv
```

* Show upstream of a branch
```shell
git rev-parse --abbrev-ref BRANCH_NAME@{upstream}
git rev-parse --abbrev-ref BRANCH_NAME@{u}
```

# Reference

* [How can I see which Git branches are tracking which remote / upstream branch?](https://stackoverflow.com/questions/4950725/)
