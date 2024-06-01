---
layout: post
title: "Display Git Tagging Message"
author: "Borting"
categories: journal
tags: [Git]
image: plum.jpg
---

Git 的 tag 分為 annotated tag 和 lightweight tag 兩類, 但只有 annotated tag 有 tagging message.

# Display Tagging Message

顯示 Tagging message
```shell
git show [TAG_NAME]
```

如果是 lightweight tag, 則會顯示 tag 指向的 commit 的 commit message.

# Reference

* [深入 Git：Git 物件儲存 - tag 物件](https://titangene.github.io/article/git-tag-object.html)
