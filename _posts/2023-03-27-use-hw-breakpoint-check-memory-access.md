---
layout: post
title: "Use HW breakpoint to Watch Kernel Data Address"
author: "Borting"
categories: journal
tags: [Linux, driver]
image: cards.jpg
---

因為 kerenl 的 tasklet 可以 access 任意的記憶體位置, 要如何在茫茫 tasklets 中找到是誰修改了某個變數在 debug 上非常重要.
還好 Linux kernel 提供了 HW breakpoint 的方式, 讓我們可以在要 monitor 的位置上註冊一個 trap.
當 kernel 對該位置讀寫時觸發 trap 來做進一步 debug, 比方說 dump stack.

# Reference

* [data\_breakpoint.c - Sample HW Breakpoint file to watch kernel data address](https://elixir.bootlin.com/linux/latest/source/samples/hw_breakpoint/data_breakpoint.c)
* [Hardware breakpoints in the Linux kernel through perf\_events](https://martin.uy/blog/hardware-breakpoints-in-the-linux-kernel-through-perf_events/)
* [Watch a variable (memory address) change in Linux kernel, and print stack trace when it changes?](https://stackoverflow.com/a/19755213)
* [Linux kernel hardware break points](https://stackoverflow.com/a/16377522)
* [register\_wide\_hw\_breakpoint continually triggers handler callback](https://stackoverflow.com/a/38283282)
* [Linux 监测内存访问的方法汇总](https://blog.csdn.net/dianzichongchong/article/details/120133833)
