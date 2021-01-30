---
layout: post
title: "Scoring Gerrit Change with Command Line"
author: "Borting"
categories: journal
tags: [Gerrit,Git]
image: plum.jpg
---

如果不想透過 Gerrit 網頁來 review code 的話, 可以透過 `git-review -d` 將 changes fetch 到本機端 review.
但若要在 command line 環境中要給 changes 打 review 分數的話, 就得依靠 [Gerrit commad line tools](https://gerrit-review.googlesource.com/Documentation/cmd-index.html#user_commands) 了.

# Gerrit Command Line Tools

Gerrit Command Line Tools 透過 SSH 下指令給 review server 上的 Gerrit daemon.
舉例來說, 可以透過以下指令列出 review server 上 host 的 projects.
```bash
# 29418 is the default port for Gerrit
$ ssh -p 29418 review.example.com gerrit ls-projects
```

## Gerrit Review

一般使用者和 Gerrit 網頁互動的情況不外乎: 給與 verified/code-review 分數, 寫 comments, 以及 rebase/abandon/cherry-pick/submit changes.
這些動作(除了 cherry-pick 外), 都可以使用 Gerrit COmmand Line Tools 的 [review command](https://gerrit-review.googlesource.com/Documentation/cmd-review.html) 達成.

Gerrit review 命令和傳統的透過 SSH 執行指令時相同.
若 review server 同時 host 多個 project, 則再多加 `--project` 區分.
```bash
$ ssh -p 29418 review.example.com gerrit review --project path/to/project --verified +1 1234,5
```

### Review Scoring

幫 change 1234 的 patchset 5 給 verified/code-review 分數.
('+'/'-' 符號要加, patchset number 一定要填.)
```bash
# verified = -1/0/+1
$ ssh -p 29418 review.example.com gerrit review --verified +1 1234,5

# code-review = -2/-1/0/+1/+2
$ ssh -p 29418 review.example.com gerrit review --code-review -1 1234,5
```

若不知道目前的 patchset number 是多少, 可以執行 `git-review -d change` 看印出的訊息.

下指令時, 也可以把最後的參數換成要被 review 的 commit ID.
```bash
# verified = -1/0/+1
$ ssh -p 29418 review.example.com gerrit review --verified +1 a5782bf

# code-review = -2/-1/0/+1/+2
$ ssh -p 29418 review.example.com gerrit review --code-review -1 a5782bf
```

### Comment Writing

注意: comment message 要用兩層 `"' '"` 包起來.
```bash
# verified = -1/0/+1
$ ssh -p 29418 review.example.com gerrit review -m "'Looks good!'" a5782bf
```

### Rebase/Abandon/Submit

Rebase, abandon 和 submit 三個命令是互斥的, 一個命令只能出現其中一個.
要注意的是, 執行 rebase, patchset number 和 commit ID 都會改變.

```bash：
# Rebase commit based on the latest HEAD of submitted branch
$ ssh -p 29418 review.example.com gerrit review --rebase a5782bf

# Abandon change
$ ssh -p 29418 review.example.com gerrit review --rebase 1234,5

# Subnit change
$ ssh -p 29418 review.example.com gerrit review --submit a5782bf
```

# Wrapper for Gerrit Review

每次下 Gerrit command line 指令都要輸入一長串 server info + proecjt name + gerrit cmd.
為了節省時間, 寫了一個 [gerrit-review wrapper](https://github.com/borting/gerrit-review): 透過 parse .git/config 檔的 remote, 把這些 server info + project name 自動補完.

`gerrit-review` wrapper 只支援部份的 Gerrit review 指令, 包含:
1. 給與 verified/review-code 分數,
2. 寫 comments, 和
3. rebase/abandon/submit changes.

指令格式和原本的 Gerrit review 指令相同, 也提供 argument 的縮寫來減少打字.
範例:
```bash
$ cd /path/to/git/repos

# -v: verified score
# -c: core-review score
# -s: submit
$ gerrit-review -v +1 -c +2 -m "'Good!'" -s a5782bf
```

`gerrit-review` 預設把 `origin` 指向的 url 視為 review server.
若要 `gerrit-review` 使用的 remote, 可以透過 `--remote` 指定.
```bash
# Review server url is stored in remote 'gerrit'
$ gerrit-review --remote gerrit -v +1 -c +2 -s a5782bf
```

# Reference

* [Gerrit commad line tools](https://gerrit-review.googlesource.com/Documentation/cmd-index.html#user_commands)
* [Using git-review to push and review changes](https://osm.etsi.org/wikipub/index.php/Using_git-review_to_push_and_review_changes#Scoring_code_review)

