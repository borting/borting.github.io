---
layout: post
title: "Git Low-level Commands"
author: "Borting"
categories: journal
tags: [Git]
image: plum.jpg
---

相對於平常在使用的 git high-level commands (或稱 porcelain commands), 這篇介紹幾個常用的 git low-level commands (或稱 plumbing commands).
這些 plumbing commands 通常比較沒有那摸麼 user-friendly, 且比較缺乏房呆機制, 但對於了解 git 底層操作與實做很有幫助.

# git cat-file

* Print commit content
```shell
git cat-file commit COMMIT_SHA1
git cat-file -p COMMIT_SHA1
```

* Print tree content
```shell
git cat-file -p TREE_SHA1
```

* Print blob content
```shell
git cat-file blob BLOB_SHA1
git cat-file -p BLOB_SHA1
```

* Print the top-level tree object of a commit or tip of a branch
```shell
git cat-file -p COMMIT_ISH
```

# git ls-tree

* Print tree/blob inside a tree object
```shell
git ls-tree TREE_ISH_SHA1
```

# git symbolic-ref

* Update `GIT_DIR/HEAD` to a branch under `GIT_DIR/refs/heads/`
```shell
git symbolic-ref HEAD refs/heads/BRANCH_NAME
```

* Note that if we want to let HEAD refers to commit (i.e. entering deteched HEAD state), we can only edit `GIT_DIR/HEAD` directly
```shell
echo COMMIT_SHA1 > GIT_DIR/HEAD
```

# git update-ref

* Update reference under `GIT_DIR/refs/`
```shell
# branch
git update-ref refs/heads/BRANCH_NAME COMMIT_ID

# lightweight tag
git update-ref refs/tags/TAG_NAME COMMIT_ID
```

* Update a branch referred by `GIT_DIR/HEAD` (this does not update the content of `GIT_DIR/HEAD`)
```shell
git update-ref HEAD COMMIT_SHA1
```

# git show-ref

* Print all refs under `GIT_DIR/refs`
```shell
git show-ref
```

# git pack-refs

* Pack all reference under `GIT_DIR/refs/heads` and `GIT_DIR/refs/tags` to `GIT_DIR/packed-refs` for search efficency
```shell
git pack-refs --all
```

* Pack all tags and non-active branches to `GIT_DIR/packed-refs` for search efficency
```shell
git pack-refs 
```

# git hash-object

* Create a blob object to `GIT_DIR/objects`
```shell
git hash-object -w FILE
```

# git update-index

* Add a new path in `GIT_DIR/index` with a new blob/tree object
```shell
git update-index --add --cacheinfo OBJ_MODE BLOB_SHA1/TREE_SHA1 PATH
```

* Update path in `GIT_DIR/index`
```shell
git update-index --cacheinfo OBJ_MODE BLOB_SHA1/TREE_SHA1 PATH
```

# git write-tree

* Creates a tree object from the state of the index if that tree doesn’t yet exist
```shell
git write-tree
```

# git read-tree

* Read an existing tree into taging area as a subtree
```shell
git read-tree --prefix=PATH TREE_SHA1
```

# commit-tree

* Create a commit object, which takes a tree objects as the top-level tree of the commits
```shell
echo 'commit_msg' | git commit-tree TREE_SHA1
```

* Create a commit object, which takes a tree objects as the top-level tree of the commits and a parent commit
```shell
echo 'commit_msg' | git commit-tree TREE_SHA1
```

# Reference

* [Git Internals - Plumbing and Porcelain](https://git-scm.com/book/en/v2/Git-Internals-Plumbing-and-Porcelain)
* [Git Internals - Git Objects](https://git-scm.com/book/en/v2/Git-Internals-Git-Objects)
* [git-update-ref - Update the object name stored in a ref safely](https://git-scm.com/docs/git-update-ref)
* [git-pack-refs - Pack heads and tags for efficient repository access](https://git-scm.com/docs/git-pack-refs)
