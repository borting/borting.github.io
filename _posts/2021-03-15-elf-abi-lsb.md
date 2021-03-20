---
layout: post
title: "Binary Compatibility: ELF, ABI, and LSB"
author: "Borting"
categories: journal
tags: [Linux]
image: Jokulsarlon.jpg
---

Binary compatibility 是指一段 code 在 A 電腦上編譯後, 在 B 電腦上能否正常執行.
影響一個程式能不能跨平台/跨作業系統執行的因素有兩個: excutable file format 和 Application Binary Interface (ABI).

# Excutable File Format

三大作業系統陣營都有定義自己的 executable file format.
* Windows - PE-COFF
* Unix - [Executable and Linkable Format (ELF)](https://en.wikipedia.org/wiki/Executable_and_Linkable_Format)
* MacOS - MACH-O

所以, 除非是 python or java 等 interpreted language, 跨作業系統是無法做到 binary compatibility.

那相同作業系統, 比方說 Unix-like systems (Linux and FreeBSD) 都是使用 ELF, 就能做到 binary compatibility 嗎?
答案是還要看 ABI 是否一致.

# Application Binary Interface

說到 ABI 時, 通常會拿 API 一起比較.
* API 定義 function 的 (1) 參數與回傳的型態, 和 (2) 參數的傳遞順序.
是兩份 **code** 之間的界面 (e.g. user-space program 和 library 之間).
是相對 high-level 的定義.
* ABI 則是定義 binary modules 之間, 也就是 program 和 library 被編譯後產生的 machine code 之間, 要如何溝通.
比方說: 要如何在 library 中找到 program 需要的 function, 呼叫 function 該如何設置 call stack, 參數要擺在 stack 的哪個位置或是要放到哪些 registers 等等.
所以, ABI 基本上是 hardware (processor) dependent.
(當然, 不同作業系統 ABI 也不同).

(這邊節錄 Wiki 對 ABI 的解釋.)
An ABI defines how data structures or computational routines are accessed in machine code, which is a low-level, hardware-dependent format.
An ABI covers the folloing topics.
* a processor instruction set (e.g. register file structure, stack organization, memory access types, etc.)
* data types layout in memory (e.g. endianness, data sizes, [alignments](https://en.wikipedia.org/wiki/Data_structure_alignment), etc.), which are processor-dependent
* [calling convention](https://en.wikipedia.org/wiki/Calling_convention) (e.g. how arguments of functions are passed, and how return value is delivered)
* [call stack](https://en.wikipedia.org/wiki/Call_stack)
* how an application should make [system calls](https://en.wikipedia.org/wiki/System_call) to the OS
* [name mangling](https://en.wikipedia.org/wiki/Name_mangling) and how to look up functions in a library
* object file format and executable file format

Note: C++ compilers adopt name mangling to realize funtion overloading.
When using `extern "C"` in a C++ program, C++ compilers will be instructed to use a standardized way of recording names that is understandable by other software, so that a program written in other language can access functions of a library written in C++.

大部分的 Unix 系統 (包含 Linux) 都有實做 [System V ABI](https://wiki.osdev.org/System_V_ABI) (裡面也定義了 ELF 格式).
但是, 每家 processor 也會針對自己的架構做出[擴充定義 (supplement)](https://wiki.osdev.org/System_V_ABI#Documents).
比方說, alignment 是 4 Bytes or 8 Bytes.
因此, 就算同樣是 Linux 系統, 在不同的 processor, 上 ABI 也是不相同的.

那用相同的 processor 和 OS (假設是相同的 Linux kernel 版本), 不同的 Linux distributions 之間 (比方說 Fedora 和 Debian) 的 binary 可以互通嗎?
答案是: 大部分可以.
但因為不同的 distribution 實做的 ABI 還是有些許差異, 所以無法做到百分之百 binary compatible.

# Linux Standard Base

談到 Linux ABI 時, 通常會順帶題到 Linux 之間的共通標準, [Linux Standard Base (LSB)](https://refspecs.linuxfoundation.org/).
LSB is a joint project by several Linux distributions to standardize the software system structure.
The standardization includes POSIX, filesystem layout (Filesystem Hierarchy Standard (FHS)), directory specifications (e.g. Freedesktop XDG Base Directory specification), and software package formats (e.g. RPM).
It is designed to be binary-compatible and produce a stable ABI (based on System V ABI and processor supplements) for independent software vendors (ISVs).

最新的 LSB 標準為 [LSB 5.0](https://refspecs.linuxfoundation.org/lsb.shtml), 但不是所有 Linux distribution 都有完全實做 LSB 標準.
事實上, 大部分 distribution 只有 LSB 4.0 compatlble.
像 Debian 就因 LSB 5.0 沒有向下相容 LSB 4.0, 所以宣佈[不再完全支援 LSB 5.0](https://lwn.net/Articles/658809/), 只支援部份的 spec 需求 (e.g. FHS, SysV init scripts).
所以, 不同的 Linux distribution 之間也沒有辦法百分之百的 binary compatible, 在 Distribution A 編譯的 program 不一定能在 Distribution B 上執行.
(舉例, 雖然 Debian/Ubuntu 可以透過 [alien](http://manpages.ubuntu.com/manpages/trusty/man1/alien.1p.html) 工具將 `.rpm` 轉成 `.deb` software package, 但 RPM format 中的部份格式是無法找到對應的 DEB format 轉換的.)

# Reference

* [Executable and Linkable Format](https://en.wikipedia.org/wiki/Executable_and_Linkable_Format)
* [ELF](https://wiki.osdev.org/ELF)
* [Application binary interface](https://en.wikipedia.org/wiki/Application_binary_interface)
* [System V ABI](https://wiki.osdev.org/System_V_ABI)
* [What is an application binary interface (ABI)?](https://stackoverflow.com/a/2456882)
* [细谈ABI (Application Binary interface)](https://juejin.cn/post/6894179449996312589)
* [Linux Standard Base](https://en.wikipedia.org/wiki/Linux_Standard_Base)
* [What are the calling conventions for UNIX & Linux system calls (and user-space functions) on i386 and x86-64](https://stackoverflow.com/a/2538212)
