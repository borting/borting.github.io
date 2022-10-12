---
layout: post
title: "Layout inside GIT\_DIR"
author: "Borting"
categories: journal
tags: [Git]
image: plum.jpg
---

`GIT_DIR` 是存放所有 objects/references 的資料夾, 同常會放在以下幾個位置
* 正常 clone 或是 init 後的 project, `GIT_DIR` 是放在 project 資料夾下的 `.git/`
* 如果是 bare/mirror clone (或是 bare init), 其會產生一個 `project_name.git` 作為 `GIT_DIR`
* 如果是透過 `git worktree add` 產生的 working directory, 其資料夾下的 `.git` 是一個 plain text 檔案, 其內容指向實際上 `GIT_DIR` 位置
```shell
gitdir: <path>
```

# GIT\_DIR/HEAD

在 non-bare repository 或是 working tree 中, 此檔案指向 (refer) 一個 branch/commit.
* 若目前 checkout 的是 local branch 會是一個指向 `GIT_DIR/refs/heads/BRANCH_NAME` 下的檔案
```
ref: refs/heads/BRANCH_NAME
```
* 若 checkout commit, tag, or remote branch, 則其內容會是 commit SHA-1 (也就是進入 detached HEAD state)

在 bare repository 中, 此檔案是 `git log` or `tig` 等指令預設讀取的 branch/commit, 基本上無其他功能.

## GIT\_DIR/FETCH\_HEAD
The SHAs of branch/remote heads that were updated during the last `git fetch`

## GIT\_DIR/ORIG\_HEAD
When doing a merge, this is the SHA of the branch you’re merging into.

## GIT\_DIR/MERGE\_HEAD
When doing a merge, this is the SHA of the branch you’re merging from.

# GIT\_DIR/index

The index file, a.k.a. staging area, records the info of directoy strucure between current working directory and the root tree of the commit refer by `GIT_DIR/HEAD`.
描述 staging area 中的目錄結構 (a binary file containing a sorted list of pathnames, each with permissions and the SHA-1 of a blob object), 作為下一個 commit 產生的依據.
可以用以下指令列出 index 中的 blobs
```shell
git ls-files -s
```

`GIT_DIR/index` 只紀錄下一個 commit 的 directory structure, 不會紀錄 changed 的內容.
也就是說, 在執行 `git add` 後, 就已經在 `GIT_DIR/objects/` 下產生了新的 blob objects, 不會產生 tree objects
`GIT_DIR/index` 則會更新這些 blob objects 的 pathname.
新的 tree objects 要等到執行 `git commit` 時才會與 commit object 一起被建立.

Bare repository 不會有 `GIT_DIR/index`, 但若有 add worktree, 則 `GIT_DIR/worktree/ID/index` 會存在.

# GIT\_DIR/config

Repository specific configuration file.
Worktree 一般來說也會共用此設定檔, 除非在 `GIT_DIR/config` 有下列設定, `GIT_DIR/worktree/ID/config.worktree` 也會被讀取
```
[core]
	repositoryformatversion = 1
[extensions]
	worktreeConfig = 1
```

# GIT\_DIR/objects/

Git objects 實際存放的資料夾
基本上每一個 `GIT_DIR` 下都會有一個 `objects` directory.
但如果是 `git worktree add` 建立出來的 worktree 的 `GIT_DIR`, 因為有設定 `GIT_COMMON_DIR` (commondir), 則該 worktree 的 `GIT_DIR` 不會有 `objects` directory 存在.

## objects/\[0-9a-f\]\[0-9a-f\]/

這是存放新建立, 尚未被 pack 的 objects, 稱為 loose objects.
Objects SHA-1 的前兩個字元作為分類, 加速 object search.

## objects/pack/

存放 packed 過後 objects 的資料夾, 包含 pack 與查找用的 index
```shell
xxxx.pack
xxxx.idx
```
For more info, see [Git’s database internals I: packed object store](https://github.blog/2022-08-29-gits-database-internals-i-packed-object-store/), [Git Internals - Packfiles](https://git-scm.com/book/en/v2/Git-Internals-Packfiles), [The Packfile](http://shafiul.github.io/gitbook/7_the_packfile.html) and [Checksums and object IDs](https://git-scm.com/docs/pack-format).

## objects/info/alternates

Paths to alternate object stores.
只有在 `git clone --shared` 和 `git clone --reference` 會使用到.

# GIT\_DIR/refs/

存放 reference 的資料夾, 方便 local/remote branch/tag name 轉換到 commit SHA-1.
只要不是 refs reachable 的 commits, 在 garbage collection 時就會被刪除

* 可以用指令列出目前 git repository 的所有 refs
```shell
git show-ref
```
* 手動修改 refs 的 raw command
```shell
# branch
git update-ref refs/heads/BRANCH_NAME COMMIT_ID

# lightweight tag
git update-ref refs/tags/TAG_NAME COMMIT_ID
```
* 要注意的是 `GIT_DIR/HEAD` 只能用下面指令修改
```shell
git symbloic-ref HEAD COMMIT_ID
```
* 部份 reference 會在執行下面二種指令後, 打包到 `GID_DIR\packed-refs`
```shell
git gc
git packed-refs
```

## refs/heads/BRANCH\_NAME

存放 local branch tip-of-the-tree commit 的 SHA-1

## refs/tags/TAG\_NAME

存放 local/remote tag 指向的 commit SHA-1 (lightweight tag) or tag object SHA-1 (annotated tag)

## refs/remotes/BRANCH\_NAME

存放 remote branch tip-of-the-tree commit 的 SHA-1

# GIT\_DIR/packed-refs

Records the same information as `refs/heads/` and `refs/tags/`

# GIT\_DIR/info

## info/exclude
Stores the exclude pattern list.
This is for local use, comparing to .gitignore which is shared by all users.

## info/attributes 
Defines which attributes to assign to a path, similar to per-directory `.gitattributes` files.

## info/sparse-checkout
This file stores sparse checkout patterns.

# GIT\_DIR/log

Records of changes made to refs are stored in this directory.
`git reflog` 指令會參考此紀錄.

## logs/refs/heads/BRANCH\_NAME
Records all changes made to the branch tip 

## logs/refs/tags/TAG\_NAME
Records all changes made to the tag

# GIT\_DIR/worktrees

Contains administrative data for linked working trees.

`GIT_DIR/worktrees/ID/` 下包含幾個與 `GIT_DIR/` 下相同功能的檔案, 其餘皆與 `GIR_DIR/` 下的檔案共用
* HEAD
* index
* logs/
* ORIG\_HEAD
* FETCH\_HEAD

`$GIT_COMMON_DIR`is a variable intended to support multiple working directories attached to a repository.
Such a repository has
* one main working directory, created by either `git init` or `git clone`,
* one or more linked working directories, created by `git worktree add`

In main working directory, `$GIT_COMMON_DIR` points to NULL and `$GIT_DIR` points to `.git` under the main working directory.
In linked working directories, `$GIT_COMMON_DIR` points to `.git` under the main working directory and `$GIT_DIR` points to `.git/worktrees/ID/` under the main working directory

These working directories share the same `$GIT_DIR` of the main working directory, and the linked working directories have some additional settings/logs stored under `GIT_DIR/worktrees/ID/`

## worktrees/ID/gitdir
Linked working tree 下的 `.git` 檔案的絕對路徑

## worktrees/ID/commondir
If this file exists, `GIT_COMMON_DIR` will be set to the path specified in this file if it is not explicitly set.

## worktrees/ID/locked 
If the file existed, the linked working cannot be pruned.

## worktrees/ID/config.worktree
Working directory specific configuration file.

# GIT\_DIR/modules
Contains the git-repositories of the submodules.

# GIT\_DIR/hooks

Customization scripts used by various Git commands.

# Reference
* [Git Repository Layout](https://git-scm.com/docs/gitrepository-layout)
* [what's inside your .git directory](https://gitready.com/advanced/2009/03/23/whats-inside-your-git-directory.html)
* [10.3 Git Internals - Git References](https://git-scm.com/book/en/v2/Git-Internals-Git-References)
* [$GIT_COMMON_DIR: a new environment variable](https://git.kernel.org/pub/scm/git/git.git/commit/?id=c7b3a3d2fe2688a30ddb8d516ed000eeda13c24e)
* [深入 Git：index 檔案](https://titangene.github.io/article/git-index.html)
* [Understanding Git — Index](https://konrad126.medium.com/understanding-git-index-4821a0765cf)
