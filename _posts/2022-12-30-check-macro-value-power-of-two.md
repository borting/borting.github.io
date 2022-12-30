---
layout: post
title: "Check Macro Value is Power of Two at Compile Stage"
author: "Borting"
categories: journal
tags: [C, CPP]
image: cards.jpg
---

在實做 ring buffer 時, 如果 buffer size 是 2 的冪次方, 就能使用 bit-wise mask 來處理 index 移動後產生的 wrap around, 減少使用的 CPU cycles.
但如果有另一個人不小心把 buufer size 改成非 2 的冪次方, bit-wise mask 的作法就會出問題, 所以大部分人怕麻煩還是會用 modulo operation 實做.
在 C/CPP 上, 如果 ring buffer size 是用 macro 定義的話, 就可以例用 preprocessor 判斷 macro 是不是 2 的冪次方, 在 compile 時決定要編譯 bit-wise mask 的實做, 還是 module operation 的實做.

# Check Power of Two

範例如下:
```c
/* Declare Ring Buffer Size */
#define BUFSIZE 1024

/* Determine Implemention of Index Incremenet */
#define IS_POWER_OF_TWO(x) ((x & (x - 1)) == 0)
#if IS_POWER_OF_TWO(BUFSIZE)
#pragma message("bit-wise mask")
#define INC_RING_INDEX(index, inc, size) (((index) + (inc)) & ((size) - 1))
#else
#pragma message("modulo op")
#define INC_RING_INDEX(index, inc, size) (((index) + (inc)) % (size))
#endif

/* Declare Buffer and Read/Write Index */
void *ring_buf[BUFSIZE] = {0};
unsigned int read = 0; write = 0;

/* Increment Index */
read = INC_RING_INDEX(read, 1, BUFSIZE);
```
