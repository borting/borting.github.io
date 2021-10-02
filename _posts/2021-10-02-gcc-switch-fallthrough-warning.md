---
layout: post
title: "GCC Switch Fallthrough Warning"
author: "Borting"
categories: journal
tags: [C, CPP]
image: Rurikoin.jpg
---

GCC 7 之後多了一個新的 warning 選項, `-Wimplicit-fallthrough`, 檢查 switch 中的每個 case 的最後一個 statement 是否為 break 或 return.
若程式邏輯需要 fall through 的話, 需要加特殊的註解, 來避免 GCC 產生 warning, 

# C or C++ (before C++17)

加入 `/* fallthrough */`, `/* FALLTHRU */`, `__attribute__ ((fallthrough));`.
```c
switch (c) {
	case 0:
		foo();
		break;
	case 1:
		bar(1);
		__attribute__ ((fallthrough));
	case 2:
		bar(2);
		/* FALLTHRU */
	case 3:
		bar(-1);
		break;
}
```

# C++17

可以使用 `[[fallthrough]];`

```cpp
switch (c) {
	case 0:
		foo();
		[[fallthrough]];
	case 3:
		bar();
		break;
}
```

# Reference

* [GCC Warning Options](https://gcc.gnu.org/onlinedocs/gcc/Warning-Options.html#index-Wimplicit-fallthrough)
* [GCC 7, -Wimplicit-fallthrough warnings, and portable way to clear them?](https://stackoverflow.com/a/45137452)
