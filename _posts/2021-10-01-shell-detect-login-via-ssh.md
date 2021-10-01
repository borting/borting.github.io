---
layout: post
title: "Detect Lgoin via SSH in Shell"
author: "Borting"
categories: journal
tags: [Linux]
image: Jokulsarlon.jpg
---

讓 shell 在 SSH 登入時換個顏色區分, 避免 SSH login 遠端時, 因為 hostname 長得太像而將指令下在錯誤的 shell.

```shell
if [ -n "$SSH_CLIENT" ] || [ -n "$SSH_TTY" ]; then
  # Change shell color
fi
```

# Reference
* [How can I detect if the shell is controlled from SSH?](https://unix.stackexchange.com/questions/9605/how-can-i-detect-if-the-shell-is-controlled-from-ssh)
