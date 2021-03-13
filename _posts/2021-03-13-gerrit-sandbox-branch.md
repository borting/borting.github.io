---
layout: post
title: "Gerrit Sandbox Branch"
author: "Borting"
categories: journal
tags: [Gerrit]
image: plum.jpg
---

使用 Gerrit 時, 開發者之間 sharing code 除了直接讓對方 fetch 自己 local 主機的 git repos 外, 也可以善用 Gerrit 的 sandbox branch 來達到分享的目的.

# Code Sharing

Gerrit 在設計時是以 code review system for Git 的觀點出發, 所有 push 上來的 code 都要經過 review 才會合回 Git, 讓其他開發者可以 fetch 的到.
但當你手中有段修改中的 patch 要分享給其他同事(e.g. 相同的 bug), 但還不適合 push 上去 Gerrit review 時, 就可以使用 Gerrit 的 sandbox branch 來做到分享 code 這件事.

Gerrit sandbox branch 是一個 per-user 的 branch space.
使用者 push 到 sandbox branch 的 code 不需要經過 review, 會直接 merge 到 repos 中.
使用者在 sandbox branch space 下也可以新增自己的 branches, 當做自己開發中的備份.

## Configuration

在 Gerrit 上的 `All-Projects` --> `Access` 的 `Create Reference` category 下新增以下 rule, 並開啟 "Force Push" 權限.
```
refs/heads/sandbox/${username}/*
```

## Usage

設定完後使用者就可以把 code push 到 sandbox/<username> 之下了.
範例如下.
```bash
$ git checkout -b sandbox/borting/foo-bar
$ git push --set-upstream origin foo-bar:sandbox/borting/foo-bar
```

其他使用者要 fecth code 時就可以直接從 Gerrit 上抓囉.
範例如下.
```bash
$ git co sandbox/borting/foo-bar
```

# Reference

* [Gerrit/personal sandbox](https://www.mediawiki.org/wiki/Gerrit/personal_sandbox)
* [Gerrit Recommended Practice: Using Sandbox Branches](https://fabrictestdocs.readthedocs.io/en/latest/Gerrit/best-practices.html#using-sandbox-branches)
* [Gerrit Code Review - Access Controls](https://gerrit-review.googlesource.com/Documentation/access-control.html)
