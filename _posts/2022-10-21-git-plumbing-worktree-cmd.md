---
layout: post
title: "Plumbing Commands to Imitate Git Worktree Add"
author: "Borting"
categories: journal
tags: [Git]
image: plum.jpg
---

`git worktree` 的出現成功取代了 `git stash` 的功能 (處理臨時出現問題時, 開一個 linked working directory 就好).
`git worktree` 也可以讓使用者免於同時 clone 好幾份相同的 project 在單一電腦上, 以及免除在不同 cloned project 間 cherry-pick 的麻煩.
不過 `git worktree add` 有一個限制: 要建立 linked working directory 的資料夾需要是 empty 或資料夾尚未建立, 否則指令會 failed.
這造成使用 `git-repo` 開啟 worktree 功能時 (`repo init --worktree`), 若有 project 是 checkout 在 root directory (i.e. 與 `.repo/` 在同一層), 則會因 `.repo/` 資料夾的存在而 failed.
要解決這個問題, 只好參考 [git worktree 的原始版本](https://github.com/git/git/blob/master/contrib/workdir/git-new-workdir), 利用 git 的 low-level commands 來達成 `git worktree add` 的效果了.

# Directories related to a Wotktree

Worktree 的操作與下列三個資料夾有關

* `$GIT_DIR`: 一般是放在 main working directory 下的 `.git/` 資料夾. 若是 bare repository, 則為 clone 後產生的 `project_name.git`.
* linked working directory (`git worktree add` 指令 checkout 的資料夾) 下的 `.git` 檔案, 其內容指向下面第三個資料夾
* `$GIT_DIR/worktress/ID/`: 保管 linked working directory 的 `HEAD`, `index`, `logs/` 等資訊的檔案與資料, 也包含 `commondir` 指向 `$GIT_DIR`, 以及 `gitdir` 指向 linked working directory 下的 `.git` 檔案.

要注意的是 `$GIT_DIR` 與 `$GIT_COMMON_DIR` 的內容是會根據現在是在 main working directory 還是 linked working directory 而不同
假設 main working directory 的位置是在 `/path/to/repos/` 的話

Working Directory | `$GIT_DIR`                          | `$GIT_COMMON_DIR`
------------------|-------------------------------------|-----------------------
Main              | `/path/to/repos/.git/`              | N/A
Linked            | `/path/to/repos/.git/worktrees/ID/` | `/path/to/repos/.git/`

# Create a Linked Working Directory via Plumbing Commands

假設 main working directory 的路徑是 `/path/to/main`, 欲建立的 linked working directory 的路徑是 `/path/to/linkedwt`

先建立 linked working directory 的 `$GIT_DIR`, 以及其內容
```shell
mkdir /path/to/main/.git/worktrees/linkedwt/
mkdir /path/to/main/.git/worktrees/linkedwt/logs

# Commit prefer to checkout in linked working directory
echo COMMIT_SHA1 > /path/to/main/.git/worktrees/linkedwt/HEAD
echo COMMIT_SHA1 > /path/to/main/.git/worktrees/linkedwt/ORIG_HEAD

# commondir usually stores relative path
echo "../.." > /path/to/main/.git/worktrees/linkedwt/commondir
echo "/path/to/linkedwt/.git" > /path/to/main/.git/worktrees/linkedwt/gitdir

# If we want to locked the linked working directory
echo "added with --lock" > locked
```

如果要 checkout 到 linked working directory 的是 branch 的話, 則 `HEAD` 和 `ORIG_HEAD` 需改成
```shell
echo `ref: refs/heads/BRANCH_NAME` > /path/to/main/.git/worktrees/linkedwt/HEAD
echo `ref: refs/heads/BRANCH_NAME` > /path/to/main/.git/worktrees/linkedwt/ORIG_HEAD
```

接著先建立 linked working directory
```shell
# Create linked working directory if not existed
mkdir /path/to/linkedwt

# Add path to GIT_DIR
echo "gitdir: /path/to/main/.git/worktrees/linkedwt" > /path/to/linkedwt/.git
```

最後再 checkout working directory 就完成了
要注意的是, 如果 /path/to/linkedwt 在 `git checkout -f HEAD` 前就存在一些在 working directory 中被 tracking 的檔案, 則 checkout 會把這些檔案覆蓋過去.
```shell
cd /path/to/linkedwt/
git checkout -f HEAD
```

# Addition Note

Linked working directory 的 `$GIT_DIR` 不一定需要放在 main working directory 下的 `.git/worktrees` 中, 也可以正常運作.
假設把 Linked working directory 的 `$GIT_DIR` 放在 `/path/to/wtstore/`
只要做下列修改 linked working directory 仍可正常運作
```shell
# Update linked working directory's .git
mv /path/to/main/.git/worktrees/linkedwt /path/to/wtstore
echo "gitdir: /path/to/wtstore/" > /path/to/linkedwt/.git
echo "/path/to/main" > /path/to/wtstore/commondir
```

# Reference
* [git-new-workdir](https://github.com/git/git/blob/master/contrib/workdir/git-new-workdir)
