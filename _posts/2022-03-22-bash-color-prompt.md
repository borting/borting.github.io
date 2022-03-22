---
layout: post
title: "Colorize Bash Prompt"
author: "Borting"
categories: journal
tags: [Bash]
image: shell.jpg
---

因為工作會同時 ssh 好幾台 server, 常常不小心在錯的 server 上下指令 ... "Orz
為了避免認錯 server, 只好在每台 server 的 bash prompt 設定不同顏色作為識別.
基於每次改顏色都在網路上亂找, 這邊紀錄一下常遇到的問題.

* Cursor 位置錯誤
通常是因為 non-printing characters 沒有用 `\[ ... \]` 包起來的關係
範例:
```shell
PS1='\[\033[0;33m\][\u@\h \w]\$ \[\033[00m\]'
```

# Reference
* [Bash/Prompt customization](https://wiki.archlinux.org/title/Bash/Prompt_customization)
* [Adding ANSI color escape sequences to a bash prompt results in bad cursor position when recalling/editing commands](https://stackoverflow.com/a/17434281)
* [Why do my Bash prompt colors make cursor appear in wrong spot](https://stackoverflow.com/a/42121882)
