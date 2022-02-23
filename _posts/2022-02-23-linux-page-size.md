---
layout: post
title: "Linux Page Size"
author: "Borting"
categories: journal
tags: [Linux]
image: Jokulsarlon.jpg
---

Linux 預設一個 page 的大小是 4KB.
Macro `PAGE_SIZE` 定義在[每個 arch 的資料夾下](https://elixir.bootlin.com/linux/v5.10/A/ident/PAGE_SIZE)
但在 embedded system 上, 為了有效使用記憶體 (e.g. 避免 internal fragmentation), 有可能會修改 `PAGE_SIZE`.
除了直接看 code 外, 這裡提供兩種方法從執行中的系統中確認 page size.

* 直接下 command
```shell
$ getconf PAGESIZE
```

* 如果 `getconf` 沒有編譯, 可以從 `/proc/meminfo` 和 `/proc/vmstat` 估算
```shell
# Get Mapped size
$ cat /proc/meminfo | grep Mapped

# Get nr_mapped
$ cat /proc/vmstat | grep nr_mapped
```
Page size 約略等於 `Mapped_size/nr_mapped`

# Reference
* [How to get linux kernel page size programmatically](https://stackoverflow.com/a/6261746)
