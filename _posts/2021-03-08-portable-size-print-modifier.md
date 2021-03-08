---
layout: post
title: "Portable Way to Print Size of C Data Structure"
author: "Borting"
categories: journal
tags: [C,Linux]
image: Rurikoin.jpg
---

`sizeof()` 回傳的資料結構是 [`size_t`](https://elixir.bootlin.com/linux/v5.11/source/include/linux/types.h#L55).
但 `size_t` 在 32-bit 和 64-bit CPU 上的長度是不一樣的: 32-bit CPU 是 `unsigned int`, 64-bit CPU 則是 `unsigned long`.
因此在選用 length modifier 印出資料結構的長度時, 就要考慮 32-bit/64-bit CPU 的 portability 的問題.

# Length Modifier for size_t

自己以前的作法是把 `sizeof()` 的輸出強制轉成 `unsigned long`, 然後在 `printf()` 使用 `%lu` linght modifier.
最近在[這篇](https://stackoverflow.com/a/5943869)看到一個更適合的作法: 使用 `%zu`.
```c
int main()
{
	printf("sizeof(void *) = %zu\n", sizeof(void *));

	return 0;
}
```

`printf()` 的 [man-page](https://man7.org/linux/man-pages/man3/printf.3.html) 也指出 `%z` 是用於列印 `size_t` 和 `ssize_t` 的 modifier.
支援 C99 的 compiler 都有支援這個 modifier.
之後寫 code 可以多注意一下.
```
z	A following integer conversion corresponds to a size_t or
	ssize_t argument, or a following n conversion corresponds
	to a pointer to a size_t argument.
```

# Reference

* [How do I print the size of int in C?](https://stackoverflow.com/a/5943869)
