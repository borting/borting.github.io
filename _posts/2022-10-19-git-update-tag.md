---
layout: post
title: "Git Update a Tag"
author: "Borting"
categories: journal
tags: [Git]
image: plum.jpg
---

若要 update 一個已經建立的 tag, 需要透過 `git tag -f` 指令更新

# Leighweight Tag

* Update the object refered by the tag
```shell
git tag -f TAG_NAME OBJECT_SHA1
```

# Annotated Tag

* Update tag message
```shell
git tag -f -a TAG_NAME TAG_NAME^{}
```

* Update the object refered by the tag
```shell
git tag -f -a TAG_NAME NEW_OBJECT_ID
```

# Tag Type Identification

* To identify whether a tag is a lightweight tag or an annotated tag, use `git show-ref -d --tags`
If it is a lightweight tag, a `refs/tags/TAG_NAME` with tag object ID will be present.
If it is a annotated taf, an additional line containg `^{}` dereference operator will be present.
``` shell
git show-ref -d --tags TAG_NAME
```

# Reference
* [How do I edit an existing tag message in Git?](https://stackoverflow.com/a/14130875)
* [Git Internals - Git References](https://git-scm.com/book/en/v2/Git-Internals-Git-References)
