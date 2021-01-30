---
layout: post
title: "Git: Retrieve Specific Version of File"
author: "Borting"
categories: journal
tags: [Gerrit,Git]
image: plum.jpg
---

This post describes how to retrieve a specific version of a gile from Git.

# Retrieve Specific Version of a File

## Using Git Checkout
```bash
# TREEISH could be a commit/tag/branch
# '--' indicates not interpret any more arguments as options
$ git checkout TREEISH -- /PATH/TO/FILE
```

## Using Git Restore (for Git 2.23+)
```bash
# TREEISH could be a commit/tag/branch
# '--' indicates not interpret any more arguments as options
$ git restore -s TREEISH -- /PATH/TO/FILE

# Restore working tree and staging area
$ git restore -s TREEISH -SW -- /PATH/TO/FILE
```

# Reference

* [How to retrieve a single file from a specific revision in Git?](https://stackoverflow.com/a/610315)

