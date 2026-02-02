---
layout: post
title: "Fix RPC Failed in Git Clone via HTTP Transport"
author: "Borting"
categories: journal
tags: [Git]
image: plum.jpg
---

# Solution

First, increase maximum size in bytes of the buffer used by smart HTTP transports when POSTing data to the remote system.
Second, adjust Git timeout settings to let Git be more patient with slow connections 

```shell
git config --global http.postBuffer 1048576000
git config --global http.lowSpeedLimit 1000
git config --global http.lowSpeedTime 20
```

# Reference
* [The remote end hung up unexpectedly while git cloning](https://stackoverflow.com/a/6849424)
* [Error Cloning Repository: RPC Failed; curl 18 transfer closed with outstanding read data remaining](https://github.com/desktop/desktop/issues/18972#issuecomment-2229030250)
