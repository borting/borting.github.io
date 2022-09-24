---
layout: post
title: "Git Packfile"
author: "Borting"
categories: journal
tags: [Git]
image: plum.jpg
---

Git 是由四大 objects - blob, tree, commit, 和 tag 組成.
其中, blob 物件存放一個檔案完整內容的 snapshot.
當一個檔案經過多次修改後, 會產生多個 blob objects.
若這些未經處理的 objects 直接存放到 Git 的 objects store, 所消耗的硬碟空間會相當可觀.
因此, Git objects store 使用的壓縮過後的儲存格式 - Git packfile.
Git packfile 除了存放部份的四大 object 外, 也會存放 deltfied 過後的 object, 來節省空間.
詳細請見 reference.

# Reference
* [Git Internals - Packfiles](https://git-scm.com/book/en/v2/Git-Internals-Packfiles)
* [The Packfile](http://shafiul.github.io/gitbook/7_the_packfile.html)
* [Checksums and object IDs](https://git-scm.com/docs/pack-format)
* [Git’s database internals I: packed object store](https://github.blog/2022-08-29-gits-database-internals-i-packed-object-store/)
