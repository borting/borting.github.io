---
layout: post
title: "Check BUG\_ON() Happens at which File and Line"
author: "Borting"
categories: journal
tags: [Linux, driver]
image: Jokulsarlon.jpg
---

最近處理一個掛在 `__timer()` 的 kernel crash, 發現 log 裡的一個有趣的地方.
看起來是可以透過開一些額外的 kenrel config 來印出更多訊息.
```shell
Kernel BUG at __mod_timer+0x2c8/0x3d0 [verbose debug info unavailable]
```

# CONFIG\_DEBUG\_BUGVERBOSE

可以在編譯時打開 `CONFIG_DEBUG_BUGVERBOSE` 這個 config, 讓 `BUG()` 和 `BUG_ON()` 印出 file name 和 line number.
打開後, 上述問題 crash 時的 log 會顯示 `BUG_ON()` 發生的行號了.
```shell
kernel BUG at kernel/time/timer.c:968!
```

# BUG\_ON()

寫 kernel code 時, 可以透過 `BUG_ON(condition)` 做 assertion.
如果 failed, 就會 dump stack 並讓當前的 process hanged.
```C
BUG_ON(condition);
```

# Reference

* [config DEBUG\_BUGVERBOSE](https://elixir.bootlin.com/linux/latest/source/lib/Kconfig.debug#L200)
* [KernelNewbies:FAQ:BUG](https://kernelnewbies.org/FAQ/BUG)
* [kernel BUG\_ON macro的实现以及brk指令触发异常后的异常处理callstack](https://www.cnblogs.com/aspirs/p/15634670.html)
