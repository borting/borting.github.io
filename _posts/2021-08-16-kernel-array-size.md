---
layout: post
title: "ARRAY\_SIZE in Linux Kernel (WIP)"
author: "Borting"
categories: journal
tags: [Linux, C]
image: Jokulsarlon.jpg
---

Linux Kernel 在計算 array 大小時, 可以使用 `ARRAY_SIZE` 這個 macro.

`ARRAY_SIZE` macro 展開如下:
```c
#define ARRAY_SIZE(arr) (sizeof(arr) / sizeof((arr)[0]) + __must_be_array(arr))
```
```c
#define __must_be_array(a) BUILD_BUG_ON_ZERO(__same_type((a), &(a)[0]))
```

有趣的是 `__must_be_array(arr)` 實做方式使用到的兩個 macro:
1. `BUILD_BUG_ON_ZERO`
2. `__same_type()`


參考以下 reference


# Reference
* [Linux Kernel: ARRAY\_SIZE()](https://frankchang0125.blogspot.com/2012/10/linux-kernel-arraysize.html)
* [Linux Kernel: BUILD\_BUG\_ON\_ZERO() / BUILD\_BUG\_ON\_NULL()](https://frankchang0125.blogspot.com/2012/10/linux-kernel-buildbugonzero.html)
* [TIL: ARRAY\_SIZE in Linux kernel](https://blog.louie.lu/2018/09/07/til-array-size-in-kernel)
* [What is “:-!!” in C code?](https://stackoverflow.com/a/9229793)
* [Other Built-in Functions Provided by GCC](https://gcc.gnu.org/onlinedocs/gcc/Other-Builtins.html)

Tag                 | Image
--------------------|--------------------------
Lagrange            | forest.jpg    
Jekyll              | forest.jpg
Gerrit              | plum.jpg
GitHub              | plum.jpg
Git                 | plum.jpg
Repo                | plum.jpg
LaTex               | light-of-world.jpg
Python              | Heidelberg.jpg
PyPI                | Heidelberg.jpg
container           | chimei-museum.jpg
Docker              | chimei-museum.jpg
Singularity         | chimei-museum.jpg
Linux               | Jokulsarlon.jpg
driver              | Jokulsarlon.jpg
C                   | Rurikoin.jpg
CPP                 | Rurikoin.jpg

# GitHub Markdown Langauge Highlight Support

Github uses [linguist](https://github.com/github/linguist/blob/master/lib/linguist/languages.yml).
