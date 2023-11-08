---
layout: post
title: "Test SSH Connection without Login"
author: "Borting"
categories: journal
tags: [Linux, Git, Gerrit]
image: Jokulsarlon.jpg
---

為了簡化每到新 build code server 上都要把所有的 gerrit server 都先 ssh access 過一次的繁瑣步驟, 最好的方式就是寫一個腳本去做這件事.

# Test SSH Connection

加 `-o StrictHostKeyChecking=no` 就不會跳出 "Are you sure you want to continue connecting?" 要求你輸入 'yes' 了

```shell
# The default git port of Gerrit is 29418
ssh -o StrictHostKeyChecking=no -p 29418 user@your.server
```

# Reference

* [Testing your SSH connection](https://docs.github.com/en/authentication/connecting-to-github-with-ssh/testing-your-ssh-connection)
* [How to accept yes from script "Are you sure you want to continue connecting (yes/no)?"](https://unix.stackexchange.com/a/458200)
