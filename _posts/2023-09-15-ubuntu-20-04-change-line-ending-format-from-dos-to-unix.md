---
layout: post
title: "dos2unix: Change Line Ending Format"
author: "Borting"
categories: journal
tags: [Linux]
image: Jokulsarlon.jpg
---

Linux 用久了, 看到純文字檔案的 line ending 是用 '\r\n', 就覺得很礙眼.
如果要一次改整個資料夾的純文字檔, 用 dos2unix

# dos2unix

```shell
find . -type f -print0 | xargs -0 dos2unix
```

# Reference

* [How can I run dos2unix on an entire directory?](https://stackoverflow.com/a/11929475)
