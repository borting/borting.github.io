---
layout: post
title: "Working with Multiple Git Repositories"
author: "Borting"
categories: journal
tags: [Git]
image: plum.jpg
---

Git 是一套 **distributed** version control system, 但大部分的 Git 使用情境還是傾向 centralized 的管理: 將 code commit 到一台 central git server 上, 並從該 server pull/fetch code 下來到 local.
真正會用到 Git 的 distributed 功能, 大部份是玩大型 open source projects (e.g. Linux) 時, 需要把 code 上到不同的 git repos 上才會遇到.

最近, 因為工作用的筆電硬碟空間不夠用了, 想把部份案子的 SDK 放到部門的 build server 上跑.
但又希望把開發中的 branch 集中在 local 管理, 方便在不同 branch 之間 cherry-pick commits.
所以就到了活用 Git 的 distributed 功能 -- git remote 的時候啦.

# Git Remote

當我們 clone 一個 repos 到 local 後, git 會預設把該 remote 設定為 `origin`.
在下 git 指令時, 若沒有指定 remote, 預設是和 `origin` remote 互動.

## Add Second Remote

以我的例子來說, 我會先在 local 筆電和 build server 上各從 clone 一個 repos.
(Build server 需要支援 SSH 登入.)
然後在 local 新增一個 remote 指向 build server 上的 repos.
```bash
$ git remote add REMOTE_NAME USER@REMOTE_HOST:/path/to/repos/on/remote/host

# Example, creating a new remote named `build`
$ git remote add build borting@build:/home/borting/repos
```

完成後, 可以透過以下指令列出目前的 remote 確認是否有新增成功.
```bash
$ git rempte -v
```

## Configure a Non-Bare Remote Repos

說明 remote repos 設定前, 簡單介紹下 bare repos 和 non-bare repos 的差異.

### Bare vs. Non-Bare Repository

Non-bare repos 就是透過 `git clone` 指令抓下來的 repos, 包含了 checkout 到 working tree 的檔案和 `.git/` 資料夾 (包含了 index 等訊息).
而 bare repos 只包含了 `.git/` 資料夾.
所以 bare repos 通常是放在 git server 上供開發者 push/fetch/pull commits 用的.

### Push Commits to Non-Bare Repos

當從 local push commits 到 remote branch 時, 如果 branch 已經被 checkout 到 working tree 的話, remote 的 non-bare git 預設會拒絕 update commit, 避免發生 working tree 與 index 不一致的情形.
類似的錯誤訊息如下:
```bash
remote: error: refusing to update checked out branch: refs/heads/master
remote: error: By default, updating the current branch in a non-bare repository
remote: error: is denied, because it will make the index and work tree inconsistent
remote: error: with what you pushed, and will require 'git reset --hard' to match
remote: error: the work tree to HEAD.
```

但以我的用途來說, 我只是單純的更新檔案到 remote.
不會在 remote server 上直接改 code, 編譯 SDK 的過程也不會改到 working tree 裡的 tracking files.
所以, 我只需要在 remote 的 repos 上新增以下設定, 允許 push commits 到已經 checkout 的 branch, 並自動 update working tree.
```bash
$ git config receive.denyCurrentBranch updateInstead
```

## Push Commits to Remote Repos

若要 push commits 到 remote repos, 可以下:
```bash
$ git push REMOTE_NAME BRANCH
```

如果 commits 經過 rebase/squash, 使得 remote branch 的 HEAD 不再是目前 commit 的 parrent, 則要 force push.
```bash
$ git push -f REMOTE_NAME BRANCH
```

其實, 就算不先新增 remote, 也可透過輸入完整的 user/host 資訊來 push.
只是每次都要打很多字很麻煩罷了.
```bash
$ git push USER@REMOTE_HOST:/path/to/repos/on/remote/host BRANCH
```

## Delete Remote

要刪除 remote 的話, 可以下指令:
```bash
$ git remote rm REMOTE_NAME
```

# Advantage of Using Git Remote

有些人習慣用 NFS mount 方式直接把 local repos 掛到 build server 上.
但在我的使用情境中, 編 SDK 會需要大量讀寫檔案.
當這些動作都需要透過網路 (NFS) 進行, 會拖慢整個編譯速度.

有些人會直接把改過的檔案手動上傳到 build server.
如果只上傳一兩個檔案, 手動可能還 ok.
但當多個檔案分散在不同資料夾時, 一個一個上傳也很花時間.

此外, 一個好的開發習慣是盡量把 develop/debug 過程都記錄下來, commit 到 local branch.
等驗證完要上傳到共用的 git server 時再將 commits 整合.
反正 git 開 local branch 不用錢, 在 build server 上的 git repos 也是我私人使用, 愛怎樣開 branch 就怎樣開囉.

# Reference

* [Let git pull changes from local repository to server repository](https://stackoverflow.com/a/21957554)
* [Git push/pull between team members local repositories](https://stackoverflow.com/a/42730901)
* [Bare vs. non-bare repositories](http://bare-vs-nonbare.gitrecipes.de/)
* [Cannot push to to a non-bare git repository at remote host](https://stackoverflow.com/a/28257982)
* [Removing a remote](https://docs.github.com/en/free-pro-team@latest/github/using-git/removing-a-remote)

