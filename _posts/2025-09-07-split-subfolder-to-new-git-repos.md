---
layout: post
title: "Split A Subfolder into A New Git Repository"
author: "Borting"
categories: journal
tags: [Git]
image: plum.jpg
---

將大型 git project 的資料夾拆成小 git project 管理, 並保留對應的 commit log

# Install git-filter-repo

```shell
sudo apt install git-filter-repo
```

# Usage

-- Clone large-size git project fitst

```shell
git clone REMOTE_GIT_REPOS_URL
```

-- Filter out a subfolder from the repository and set the subfolder as new root folder

```shell
git filter-repo --subdirectory-filter SUB_FOLDER_NAME
```

-- Create a new git repos and fetch filtered repos to the new git repos

# Reference

* [Splitting a subfolder out into a new repository](https://docs.github.com/en/get-started/using-git/splitting-a-subfolder-out-into-a-new-repository?platform=linux)
* [Github - newren/git-filter-repo ](https://github.com/newren/git-filter-repo)
