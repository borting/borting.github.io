---
layout: post
title: "Data Model and Size of Data Types"
author: "Borting"
categories: journal
tags: [Linux,C]
image: Jokulsarlon.jpg
---

[上一篇]({% post_url 2021-03-27-netdev-counter-wrap-around %})討論 `struct net_device_stats` 時有提到在 32-bit 和 64-bit Linux 上的 `unsigned long` 長度是不同的.
前者是 32 bits, 後者是 64 bits.
然而, 在 Win32 和 Win64 上, `unsigned ling` 卻都是 32 bits.
造成這些差異的原因在於: 從 32-bit CPU 演進到 64-bit CPU 時, 作業系統在考慮可移植性等因素後, 選擇了不同的 data model 所致.

# Size of Data Types in C Standard

在討論 data model 前先解釋一個疑惑, 難道 C standard 沒有定義 data types 的長度嗎?

No, 真的沒有.
C99 沒有硬性規定 data type 的長度, 只要求 `sizeof(char) <= sizeof(short) <= sizeof(int) <= sizeof(long)`.
所以在不同的 CPU 和作業系統上, data type 的長度不一定會是相同的.

要知道 data type 的長度, 一個簡單的方法是利用 LDD3 的 [`datasize.c`](https://github.com/martinezjavier/ldd3/blob/master/misc-progs/datasize.c) 判斷.
```c
#include <stdio.h>
#include <sys/utsname.h>
#include <linux/types.h>

int main(int argc, char **argv)
{
    struct utsname name;

    uname(&name); /* never fails :) */
    printf("arch   Size:  char  short  int  long   ptr long-long "
	   " u8 u16 u32 u64\n");
    printf(       "%-12s  %3i   %3i   %3i   %3i   %3i   %3i      "
	   "%3i %3i %3i %3i\n",
	   name.machine,
	   (int)sizeof(char), (int)sizeof(short), (int)sizeof(int),
	   (int)sizeof(long),
	   (int)sizeof(void *), (int)sizeof(long long), (int)sizeof(__u8),
	   (int)sizeof(__u16), (int)sizeof(__u32), (int)sizeof(__u64));
    return 0;
}
```

在 i386 CPU 上的 32-bit Linux 中執行 `datasize.c` 的結果.
```
arch   Size:  char  short  int  long   ptr long-long  u8 u16 u32 u64
i386            1     2     4     4     4     8        1   2   4   8
```

在 x86_64 CPU 上的 64-bit Linux 中編譯並執行相同程式, 可以發現 `long` 和 `ptr` 的長度變成 64 bits 了.
```
arch   Size:  char  short  int  long   ptr long-long  u8 u16 u32 u64
x86_64          1     2     4     8     8     8        1   2   4   8
```

值得注意的是, 在 64-bit Linux 中編譯 `datasize.c` 時多加 `-m32` 參數, 則會得到不一樣的結果.
`long` 和 `ptr` 的長度都為 32 bits, 與在 i386 CPU 上的 32-bit Linux 的結果相同.
(原因後面再說.)
```
arch   Size:  char  short  int  long   ptr long-long  u8 u16 u32 u64
x86_64          1     2     4     4     4     8        1   2   4   8
```

(若想知道更多 CPU / 作業系統上執行的結果, 可參考 [LDD3 Ch.11](https://static.lwn.net/images/pdf/LDD3/ch11.pdf) 的說明).

# Data Model

實際上, data type 的長度是由作業系統使用的 data model 決定的.
即使是相同的作業系統, 在不同 CPU 上, 也可能因為 [ABI]({% post_url 2021-03-15-elf-abi-lsb %}) 不同而使用不同的 data model.

## ILP32, LP64, and LLP64

現在比較常見的 data model 有三個: ILP32, LP64, LLP64.
其中 I 代表 `int` type, L 代表 `long` type, LL 代表 `long long` type, P 代表 `pointer` type.
ILP32 data model 就是指 `int`, `long`, 和 `pointer` 三個型態的長度都是 32 bits.

## Size of Data Types

下表整理 ILP32, LP64, LLP64 和 ILP64 model 中, data type 的長度.

C Data Type | ILP32 | LP64 | LLP64 | ILP64
------------|-------|------|-------|-------
char        | 8     | 8    | 8     | 8
short       | 16    | 16   | 16    | 16
int         | 32    | 32   | 32    | 64
long        | 32    | 64   | 32    | 64
long long   | 64    | 64   | 64    | 64
pointer     | 32    | 64   | 64    | 64
enum        | 32    | 32   | 32    | ?
float       | 32    | 32   | 32    | ?
double      | 64    | 64   | 64    | ?

ILP32 主要是使用在 32-bit CPU (e.g. i386) 上.
`int`, `long`, 和 `pointer` type 的長度都是 32 bits.
切換到 64-bit CPU 後, 為了提供更大的地址空間, `pointer` type 的長度擴大成 64 bits.
其餘 data type, 則因為各種考量 (主要是 porting 32-bit program 到 64-bit OS 的 effort), 不一定都擴展成 64 bits, 所以就分成了 LP64 和 LLP64 兩派.
(ILP64 沒有被任何作業系統採用.)
LP64 是 `long` 和 `pointer` 的長度都為 64 bits.
LLP64 的 `long` type 長度是 32 bits, 除 `pointer` 以外的 data type 長度都與 ILP32 相同.

## Operating System and Data Model

LP64 和 LLP64 model 各有其支持者.
下表整理常見作業系統使用的 data model.

Data Model | OS
-----------|-------------------------
ILP32      | Win32, i386 Linux & OSX
LP64       | x86-64 Linux & OSX
LLP64      | Win64

主要的作業系統在 32-bit CPU (這裡只考慮 i386) 都是使用 ILP32 model.
切換到 64-bit CPU (x86_64 or amd64) 時, Unix-like system 選擇了 LP64 model, 而 Windows 選擇了 LLP64 model.

Win64 選擇 LLP64 的主要考量是希望 Win32 下寫的程式可以直接 porting 到 Win64 上.
藉由保持 `int` 和 `long` type 的長度不變, 並新增長度為 64 bits 的 `long long` type, 將 porting 的 effort 降到最低 (只須考慮 `pointer` type 長度差異造成的影響).

[這篇](http://www.unix.org/version2/whatsnew/lp64_wp.html)和[這篇](http://www.unix.org/whitepapers/64bit.html)介紹了 Unix 選則 LP64 model 的理由.
不考慮 ILP64 model 是因為若把 `int` type 的長度也定義成 64 bits 的話, 會產生更多的 porting effort.
不用 LLP64 model 的理由是要新增 non-portable 的 `long long` type.
(Unix 決定使用 LP64 是在 1997 年, 而 `long long` type 在 C99 才被正式納入 standatd.)
但缺點是 porting 在 ILP32 model 作業系統中寫的程式到使用 LP64 model 的作業系統上時, 除了 `pointer` type 外, 要多考慮 `long` type 長度的差異.

## x32 ABI

雖然 x86_64 的 64-bit Linux 選用了 LP64 model, 但部份 Linux distributions 也提供了 [x32 ABI](https://wiki.debian.org/X32Port), 讓在 i386 32-bit Linux 中開發的程式可以直接在 x86_64 64-bit Linux 上執行.
前面範例中編譯 `datasize.c` 時多下 `-m32` 參數後仍可在 64-bit Linux 上執行, 也是因為 64-bit Linux 上有支援 x32 ABI.
透過 x32 ABI 執行的程式, `int`, `long` 和 `pointer` 的長度都是 32 bits.
所以執行解果看到的 data type 長度才會與 ILP32 model 一致.

透過 x32 ABI 執行程式雖然可定址的範圍縮小了, 但也相對地減少了程式的大小.
此外, x32 ABI 保留了 x86_64 CPU 較佳的浮點運算能力和可以使用更多的 registers 作為 function 參數傳遞的優點.
當然, 也省去了許多 poring effort.

# Conclusion

C data type 的長度不是固定的.
C99 standard 只規定了 data types 之間的相對長度關係.
所以, 以後被問某個變數的長度是多少時, 應該要先反問**是在哪個 data model** 上?

至於 porting ILP32 model 上開發的程式到 LP64 model 上要注意哪些事項?
或是如何開發在 ILP32 和 LP64 model 上都能編譯的程式?
(這裡指的是程式碼的邏輯在 ILP32 和 LP64 上重新編譯都不會出錯.)
就等下一篇再來解釋吧.

# Reference

* [Data Models and Word Size](http://nickdesaulniers.github.io/blog/2016/05/30/data-models-and-word-size/)
* [Linux Device Driver 3: Data Types in the Kernel](https://static.lwn.net/images/pdf/LDD3/ch11.pdf)
* [一個長整數各自表述 (in 64-bit system)](https://dada.tw/2008/04/18/85/)
* [資料模型 Data Model - LP32 ILP32 LP64 ILP64 LLP64](https://tclin914.github.io/d7ed614/)
* [数据模型 LP32 ILP32 LP64 LLP64 ILP64](https://www.cnblogs.com/lsgxeva/p/7614856.html)
* [Microsoft Windows: Data Type Ranges](https://docs.microsoft.com/en-us/cpp/cpp/data-type-ranges?view=msvc-160)
* [64-Bit Programming Models: Why LP64?](http://www.unix.org/version2/whatsnew/lp64_wp.html)
* [64-bit and Data Size Neutrality](http://www.unix.org/whitepapers/64bit.html)
* [Wikipedia: 64-bit data models](https://en.wikipedia.org/wiki/64-bit_computing#64-bit_data_models)
* [Wikipedia: x32 ABI](https://en.wikipedia.org/wiki/X32_ABI)
