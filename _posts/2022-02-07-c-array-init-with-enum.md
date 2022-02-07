---
layout: post
title: "Array Initialization with Named Initializers"
author: "Borting"
categories: journal
tags: [C]
image: Rurikoin.jpg
---

C99 之後提供了 named initializers 功能.
在初始化 array 時, 可以透過 enumuraion constant 來設定 array 特定欄位的初始值.
但要注意的是, C++ 不支援此功能.

# Named Initializer

舉個例子:
```C
enum Color_t {
    RED,
    YELLOW,
    BLUE = 4
};

static const int Color2Number[] = {
        [RED] = 9,
        [YELLOW] = 11,
        [BLUE] = 2
};
```

# Reference
* [Array initialization with enum indices in C but not C++](https://eli.thegreenplace.net/2011/02/15/array-initialization-with-enum-indices-in-c-but-not-c/)
