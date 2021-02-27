---
layout: post
title: "Git Archive 筆記"
author: "Borting"
categories: journal
tags: [Git]
image: plum.jpg
---

紀錄一下 `git archive` 相關的指令與設定.

# Commands

## Basic

基本的壓縮指令

```bash
$ git archive --format=tar.gz COMMITISH --output=/PATH/TO/FILE.tar.gz
$ git archive --format=tar.xz COMMITISH --output=/PATH/TO/FILE.tar.xz
$ git archive --format=zip COMMITISH --output=/PATH/TO/FILE.zip
```

若要指定壓縮後的 root dir name, 可以追加 `--prefix=`

```bash
$ git archive --format=tar.gz --prefix=ROOT_DIR_NAME COMMITISH --output=/PATH/TO/FILE.tar.gz
```

## Useful Alias

若不想打一長串指令可以在 `~/.gitconfig` 設定 alias

```gitconfig
[alias]
  compress = "!f() {  git archive --format=$1 --prefix=${2%%.*}/ $3 --output $4; }; f"
  xz = "!f() { if [ -z "$2" ]; then git compress tar.xz $(basename ${1}) HEAD $1; else git compress tar.xz $(basename ${1}) $2 $1; fi; }; f"
  gz = "!f() { if [ -z "$2" ]; then git compress tar.gz $(basename ${1}) HEAD $1; else git compress tar.gz $(basename ${1}) $2 $1; fi; }; f"
  zip = "!f() { if [ -z "$2" ]; then git compress zip $(basename ${1}) HEAD $1; else git compress zip $(basename ${1}) $2 $1; fi; }; f"
```

使用方法

```bash
# 壓縮後的 root dir name 為壓縮檔的 base name (i.e. FILE)
$ git gz COMMITISH /PATH/TO/FILE.tar.gz
$ git xz COMMITISH /PATH/TO/FILE.tar.xz
$ git zip COMMITISH /PATH/TO/FILE.zip
```

## File Exclusion

若希望在打包時移除特定檔案或資料夾, 可在 repos 裡新增 `.gitattributes` 設定 export-ignore rules.
```gitconfig
# Ignore single file
.gitignore export-ignore
.gitattributes export-ignore

# Ignore files mathcing patter
classified_file_*

# Ignore a dir
classified_dir/** export-ignore
```

在 repos 下 `.gitattributes` 是設計成共通的的 rule, 所以 `.gitattributes` 的 exort ignore list 需要被 commit 後才會生效.
若只是在單一 repos 下設定個人用的 rule, 則應該把 export ignore list 加在 repos 的 `.git/info/attributes`.
若是要設定 global 的 export ignore list, 則要加在 `~/.config/git/attributes`.

# Refenece

* [Using git attributes to exclude files from your release](https://www.pixelite.co.nz/article/using-git-attributes-exclude-files-your-release/)
* [Git - gitattributes Documentaion](https://git-scm.com/docs/gitattributes)
* [https://news.ycombinator.com/item?id=24832427](https://news.ycombinator.com/item?id=24832427)
* [How to ignore files/directories in “git archive” and only create an archive of a subdirectory?](https://stackoverflow.com/questions/52804334/how-to-ignore-files-directories-in-git-archive-and-only-create-an-archive-of-a)
