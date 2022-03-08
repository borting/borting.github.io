---
layout: post
title: "Linux Memory Management"
author: "Borting"
categories: journal
tags: [Linux]
image: Jokulsarlon.jpg
---

`highmem`: The part of (physical) memory which is not covered by a permanent mapping.
This part of memory are used in user-space memory or kernel virtual mapping


# Symbols from Kconfig or Macro Definition


* `PAGE_OFFSET`: creates a virtual memory space at the addresses above it, for the kernel to live in.
Only on 32-bit proecess (`CONFIG_X86_32` or `CONFIG_ARM`)
So, generally, the kernel lives in `0xC0000000-0xFFFFFFFF`

* `PHYS_OFFSET`: Physical start address of the first bank of RAM. (On ARM)
For kernel logical address, its physcal address on RAM is `kernel logical address - PAGE_OFFSET + PHYS_OFFSET`

* `TASK_SIZE`: Size of user task space


# Terminololgy

Address spaces
* Kernel logical address
* Kernel virtual address
* User virtual address


# Videos

* David Black-Schaffer's [Virtual Memory](https://www.youtube.com/watch?v=qcBIvnQt0Bw&list=PLiwt1iVUib9s2Uo5BeYmwkDFUh70fJPxX) series.

* [Introduction to Memory Management in Linux](https://www.youtube.com/watch?v=7aONIVSXiJ8)

# Reference
* [The Kernel Address Sanitizer (KASAN)](https://docs.kernel.org/dev-tools/kasan.html)
* [Kernel Doc -- Configuration for Memory on ARM](https://www.kernel.org/doc/Documentation/arm/Porting)
* [How the ARM32 kernel starts](https://people.kernel.org/linusw/how-the-arm32-kernel-starts)
* [The history of Unix's confusing set of low-level ways to allocate memory](https://utcc.utoronto.ca/~cks/space/blog/unix/SbrkVersusMmap)
* Setting Up the ARM32 Architecture [part 1](https://people.kernel.org/linusw/setting-up-the-arm32-architecture-part-1) and [part 2](https://people.kernel.org/linusw/setting-up-the-arm32-architecture-part-2)
