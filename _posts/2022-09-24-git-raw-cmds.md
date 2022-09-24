---
layout: post
title: "Git Raw Commands: cat-file and rev-parse"
author: "Borting"
categories: journal
tags: [Git]
image: plum.jpg
---

紀錄一下在讀 [Git’s database internals I: packed object store](https://github.blog/2022-08-29-gits-database-internals-i-packed-object-store/) 時學到的一些 git 底層指令

# cat-file

`git cat-file` 是用來 dump git objects 的 type/size/content 等資訊:
```shell
# Print type of a object
git cat-file -t OBJECT_ID

# Print content of a object
git cat-file -p OBJECT_ID
```

# rev-parse

```shell
# Get sha-1 ID for an object
git rev-parse BEANCH_NAME
git rev-parse TAG_NAME
```

# rev-list

Lists commit objects in reverse chronological order
```shell
# List the object ID from COMMITISH to first commit
git rev-list COMMITISH

# List the object ID from COMMITISH_A to COMMITISH_B
git rev-list COMMITISH_A...COMMITISH_B
```

# rev-parse


# Misc

* Get the commit of a commit-ish
```shell
git cat-file -p TAG^{tree}
git cat-file -p BRANCH^{tree}
```

* Get the root tree of a commit-ish
```shell
git cat-file -p COMMIT^{tree}
git cat-file -p TAG^{tree}
git cat-file -p BRANCH^{tree}
```

# Reference
* [Git’s database internals I: packed object store](https://github.blog/2022-08-29-gits-database-internals-i-packed-object-store/)
