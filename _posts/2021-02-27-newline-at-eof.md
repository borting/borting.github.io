---
layout: post
title: "Newline at End of File"
author: "Borting"
categories: journal
tags: [Linux]
image: Jokulsarlon.jpg
---

最近收到一個 [pull request](https://github.com/borting/nctu-thesis/pull/16) 幫忙補了幾個檔案的 EOF.
Github 網頁 diff 看不出來改了哪, 把 commit 拉下來後才看到多補了一個 `newline` character (`\n`).
想說之前沒加 `\n` 也活得好好的, 所以好奇 Google 了一下差在哪.

# Line Definition in POSIX Standard

依照 [POSIX 標準](https://pubs.opengroup.org/onlinepubs/9699919799/basedefs/V1_chap03.html#tag_03_206)的定義, line 是 `A sequence of zero or more non- <newline> characters plus a terminating <newline> character.`
也就是說每一行的結尾都應該要有 `\n`, 才能視為完整的一行.

如果在檔案最後一行沒有 `\n`, 在某些情況下可能會有顯示問題.
比方說用 `cat` 看檔案時, 因為少了 `\n`, 下一行命令輸入會 prompt 在檔案內容的結尾, 而不是新的一行.
```bash
borting@borting:~$ cat test.txt
123 borting@borting:~$
```
缺少 `\n` 更麻煩的情況有可能造成 parsing 錯誤, 所以像在 ISO C89 就有規定檔案結尾一定要是 `\n`, 否則 gcc 會報 warning.

## EOF Requrirements on Different OS

對 Windows 系統來說, 檔案就不要求一定要以 `\n` 結尾.
所以 `nctu-thesis`(https://github.com/borting/nctu-thesis) 有些檔案少了 `\n` 結尾, 可能是那些檔案最初是在 Windows 下寫的關係.

[這一篇](https://stackoverflow.com/a/16224292)也比較不同 Editor 對檔案結尾的 `\n` 顯示的差異.
看起來只有 `vim` 和 `nano` 這兩個純血 UNIX Editor 不會多顯示一行, 其他 Editor 都會因為檔案結尾的 `\n` 而多顯示一行空白行在結尾.

# Reference

* [Why should text files end with a newline?](https://stackoverflow.com/a/729795)
* [Why do I need vim in binary mode for 'noeol' to work?](https://stackoverflow.com/a/16224292)
* [Re: wny does GCC warn about "no newline at end of file"?](https://gcc.gnu.org/legacy-ml/gcc/2003-11/msg01568.html)
