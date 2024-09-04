---
layout: post
title: "Update Git Submodule without Checkout"
author: "Borting"
categories: journal
tags: [Git]
image: cards.jpg
---

Update a git submodule without checking out the submodule in advance.

# Command

```shell
git update-index --add --cacheinfo 160000,SUBMODULE_SHA1,SUBMODULE_PATH
```

-- 160000 is mode of a git directoy
-- `SUBMODULE_SHA1` is the commit of submodule we want update to
-- `SUBMODULE_PATH` is the path to submodule in the git repository

# Reference
* [How do I update a git submodule without checking it out](https://stackoverflow.com/a/64950161)
