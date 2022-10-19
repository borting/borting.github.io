---
layout: post
title: "Git Tag a Blob Object"
author: "Borting"
categories: journal
tags: [Git]
image: plum.jpg
---

前幾天在[這篇文章](https://git-scm.com/book/en/v2/Git-Internals-Git-References)中看到 git 可以 tag blob/tree objects.
紀錄一下要如何 tag 一個 blob object.

# Get Blob Object SHA1

* Get blob object's SHA1 of a tracked file
```shell
git rev-parse COMMIT_ISH:PATH/TO/FILE
```

* Create a new blob object from a non-tracked file
```shell
git hash-object -w PATH/TO/FILE
```

* Create a new blob object from stdin
```shell
cat PATH/TO/FILE | git hash-object -w --stdin
echo 'blob content' | git hash-object -w --stdin
```

# Tag a Blob Object

* Create a tag refers a blob object
```shell
# lightweight
git tag TAG_NAME BLOB_SHA1

# annotated
git tag -a TAG_NAME BLOB_SHA1
```

# Retrieve Tag Content

* Such a tag cannot be checkout, since it refers to a blob object rather than a commit.
To get the blob content
```shell
git cat-file -p TAG_NAME^{}
```

# Reference
* [Git Internals - Git Objects](https://git-scm.com/book/en/v2/Git-Internals-Git-Objects)
* [Git Internals - Git References](https://git-scm.com/book/en/v2/Git-Internals-Git-References)
* [How to get object ID of a file at a specific commit?](https://stackoverflow.com/a/47542601)
