---
layout: post
title: "Git Commit-ish and Tree-ish"
author: "Borting"
categories: journal
tags: [Git]
image: plum.jpg
---

在查 git 指令的時候, 常常看到 `<commit-ish>` 和 `<tree-ish>` 這兩個名詞.
之前一直誤以為是指 git 的 `commit` 和 `tree` object, 直到最近在 stackoverflow 看到了[這一篇解釋](https://stackoverflow.com/a/18605496)才豁然開朗.

# Commit-ish vs. Tree-ish

* `<Commit-ish>` 是指 git 中的任一 identifiers (commit objects, tags, branches, etc.), 其最終能指向並操作一個 commit object.
* `<Tree-ish>` 是指 git 中的任一 identifiers, 其最終能指向並操作一個 tree object.
除了 tree object 之外, 因為 commit object 也指向 root directory 的 tree object, 所以 `<tree-ish>` 也包含所有的 `<commit-ish>` identifiers.


## Commit-ish/Tree-ish

Commit-ish/Tree-ish        | Examples
---------------------------|------------------------------------------
 1. \<sha1\>               | dae86e1950b1277e545cee180551750029cfe735
 2. \<describeOutput\>     | v1.7.4.2-679-g3bee7fb
 3. \<refname\>            | master, heads/master, refs/heads/master
 4. \<refname\>@{\<date\>} | master@{yesterday}, HEAD@{5 minutes ago}
 5. \<refname\>@{\<n\>}    | master@{1}
 6. @{\<n\>}               | @{1}
 7. @{-\<n\>}              | @{-1}
 8. \<refname\>@{upstream} | master@{upstream}, @{u}
 9. \<rev\>^               | HEAD^, v1.5.1^0
10. \<rev\>~\<n\>          | master~3
11. \<rev\>^{\<type\>}     | v0.99.8^{commit}
12. \<rev\>^{}             | v0.99.8^{}
13. \<rev\>^{/\<text\>}    | HEAD^{/fix nasty bug}
14. :/\<text\>             | :/fix nasty bug


## Tree-ish Only

Tree-ish                   | Examples
---------------------------|------------------------------------------
1. \<rev\>:\<path\>        | HEAD:README, :README, master:./README
2. :\<n\>:\<path\>         | :0:README, :README

# Reference

* [What does tree-ish mean in Git?](https://stackoverflow.com/a/18605496)
* [Git Identifier Terminology](https://mirrors.edge.kernel.org/pub/software/scm/git/docs/#_identifier_terminology)

