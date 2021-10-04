---
layout: post
title: "Volatile Qualifier in C"
author: "Borting"
categories: journal
tags: [C]
image: cards.jpg
---

Volatile qualifier 宣告一個 variable 的值是會不斷改變.
因此 compiler 不能將 volatile variable 做最佳化處理, 每次都要從 memory 讀取最新的值.

# Volatile Keyword

Quote 一下 [這篇文章](https://www.keil.com/support/man/docs/armcc/armcc_chr1359124222941.htm):
* The volatile variable can be modified at any time externally to the implementation, for example, by the operating system, by another thread of execution such as an interrupt routine or signal handler, or by hardware.
* The compiler cannot perform optimizations on the variable, for example, caching its value in a register to avoid memory accesses.

In practice, you must declare a variable as volatile whenever you are:
* Accessing memory-mapped peripherals.
* Sharing global variables between multiple threads.
* Accessing global variables in an interrupt routine or signal handler.

# Reference
- [Code metricsHomeLoop unrolling in C code Compiler optimization and the volatile keyword](https://www.keil.com/support/man/docs/armcc/armcc_chr1359124222941.htm)
