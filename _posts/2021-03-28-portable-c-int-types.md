---
layout: post
title: "Portable C Types on 32-/64-bit Linux"
author: "Borting"
categories: journal
tags: [Linux,C,CPP]
image: Rurikoin.jpg
---

[上一篇]({% post_url 2021-03-28-data-model %})討論 data model 時談到 Unix-like OS 和 Windows 因為 porting effort 的考量, 在 x86_64 CPU 上開發時採用了不同的 data model: LP64 和 LLP64.
Unix-like OS 的開發者將在 i386 Linux (ILP32 model) 下撰寫的 code porting 到 x86_64 64-bit Linux (LP64 model) 上時, 需考慮到 `long` 和 `pointer` 長度造成的差異, 否則就有機會發生不可預期的行為.
(例如, [device counter 溢位]({% post_url 2021-03-27-netdev-counter-wrap-around %}).)
這一篇整理一下 porting 32-bit Linux program 到 64-bit Linux 的注意事項.

# Fixed-width Integer Types

避開 data type 長度在不同 data model 上的差異最簡單的方法就是使用 fixed-width integer types.

## User-space Programs

在 Linux 上開發 user-space program 時 include `<stdint.h>`, 就可以使用以下固定長度的 integer types.
以下參考 [cppreference.com 的說明](https://en.cppreference.com/w/c/types/integer).

```c
// Integer Types
int8_t    s8_data;   // 8-bit signed interger
int16_t   s16_data;  // 16-bit signed interger
int32_t   s32_data;  // 32-bit signed interger
int64_t   s64_data;  // 64-bit signed interger
uint8_t   u8_data;   // 8-bit unsigned interger
uint16_t  u16_data;  // 16-bit unsigned interger
uint32_t  u32_data;  // 32-bit unsigned interger
uint64_t  u64_data;  // 64-bit unsigned interger

// Pointer Types
intptr_t  iptr;  // signed integer type capable of holding a pointer
uintptr_t uptr;  //	unsigned integer type capable of holding a pointer
```

使用 `printf()` 和 `scanf()` 時, 要 include `<inttypes.h>` 使用 fixed-width data types 的 format specifier.
```c
#include <stdio.h>
#include <inttypes.h>  // include <stdint.h>
 
int main(void)
{
	printf("%zu\n", sizeof(int64_t));
	printf("%s\n", PRId64);
	printf("%+"PRId64"\n", INT64_MIN);
	printf("%+"PRId64"\n", INT64_MAX);

	int64_t n = 7;
	printf("%+"PRId64"\n", n);
}
```

`<stdint.h>` 和 `<inttypes.h>` 定義的 data types 和 macros 都有定義 C99 中.
在 Windows 上也可以直接使用這些 fixed-width data types 撰寫 portable 的 user-sace 程式.
此外, 在 Win32 和 Win64 也可以使用以下 fixed-width data types 宣告變數.
```c
INT8      s8_data;   // 8-bit signed integer
INT16     s16_data;  // 16-bit signed integer
INT32     s32_data;  // 32-bit signed integer
INT64     s64_data;  // 64-bit signed integer
UINT8     u8_data;   // 8-bit unsigned integer
UINT16    u16_data;  // 16-bit unsigned integer
UINT32    u32_data;  // 32-bit unsigned integer
UINT64    u64_data;  // 64-bit unsigned integer
```

## Kernel Development

Linux kernel 也定義了 fixed-width data types 來協助撰寫 portable code.
要使用這些 data types, 需要 include [`<linux/types.h>`](https://elixir.bootlin.com/linux/v5.11/source/include/linux/types.h).
```c
s8    s8_data;   // 8-bit signed interger
s16   s16_data;  // 16-bit signed interger
s32   s32_data;  // 32-bit signed interger
s64   s64_data;  // 64-bit signed interger
u8    u8_data;   // 8-bit unsigned interger
u16   u16_data;  // 16-bit unsigned interger
u32   u32_data;  // 32-bit unsigned interger
u64   u64_data;  // 64-bit unsigned interger
```

若撰寫的 code (比方說 header files) 有機會被 user-space program 使用的話, 則應該使用 exportable 的 fixed-width data types.
```c
__s8    s8_data;   // 8-bit signed interger
__s16   s16_data;  // 16-bit signed interger
__s32   s32_data;  // 32-bit signed interger
__s64   s64_data;  // 64-bit signed interger
__u8    u8_data;   // 8-bit unsigned interger
__u16   u16_data;  // 16-bit unsigned interger
__u32   u32_data;  // 32-bit unsigned interger
__u64   u64_data;  // 64-bit unsigned interger
```

# Reference

* [一個長整數各自表述 (in 64-bit system)](https://dada.tw/2008/04/18/85/)
* [difference between stdint.h and inttypes.h](https://stackoverflow.com/a/9162072)a
* [C Programming/stdint.h](https://en.wikibooks.org/wiki/C_Programming/stdint.h)
* [Writing Portable Device Drivers](https://www.linuxjournal.com/article/5783)
