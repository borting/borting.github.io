---
layout: post
title: "Show C Macro Value in Compile Log"
author: "Borting"
categories: journal
tags: [C, CPP]
image: Rurikoin.jpg
---

工作上要維護的那份 driver 太亂, 常常搞不清楚一個 macro 到底有沒有被定義, 或是被定義成什麼值.
在 stack overflow 看到一個利用 gcc stringification 的方法, 在 compile log 裡直接印出 macro 值.

# Use GCC Stringification to Print Macros

範例如下:
```c
/* definition to expand macro then apply to pragma message */
#define VALUE_TO_STRING(x) #x
#define VALUE(x) VALUE_TO_STRING(x)
#define VAR_NAME_VALUE(var) #var "="  VALUE(var)

/* Some example here */
#define DEFINED_INT 3
#define DEFINED_STR "ABC"
#define DEFINED_BUT_NO_VALUE
#define DEFINED_FUNC(a, b) ((a > b) ? a, b)

#pragma message(VAR_NAME_VALUE(DEFINED_INT))
#pragma message(VAR_NAME_VALUE(DEFINED_STR))
#pragma message(VAR_NAME_VALUE(DEFINED_BUT_NO_VALUE))
#pragma message(VAR_NAME_VALUE(DEFINED_FUNC))
#pragma message(VAR_NAME_VALUE(NOT_DEFINED))
```

Compile log 輸出的如下:
```
test.c:23:9: note: #pragma message: DEFINED_INT=3
 #pragma message(VAR_NAME_VALUE(DEFINED_INT))
         ^
test.c:24:9: note: #pragma message: DEFINED_STR="ABC"
 #pragma message(VAR_NAME_VALUE(DEFINED_STR))
         ^
test.c:25:9: note: #pragma message: DEFINED_BUT_NO_VALUE=
 #pragma message(VAR_NAME_VALUE(DEFINED_BUT_NO_VALUE))
         ^
test.c:26:9: note: #pragma message: DEFINED_FUNC=DEFINED_FUNC
 #pragma message(VAR_NAME_VALUE(DEFINED_FUNC))
         ^
test.c:27:9: note: #pragma message: NOT_DEFINED=NOT_DEFINED
 #pragma message(VAR_NAME_VALUE(NOT_DEFINED))
         ^
```

說明:
-- 如果 macro 有被定義成一個數字或字串, 則等號後會輸出數字或字串
-- 如果 macro 有被 defined 但沒有 value (e.g. kernel config), 則等號後為 emty
-- 如果 macro 不存在, macro 被 undefined, 或是 macro 被定義成一個 function, 則等號後會輸出和 macro 的名稱

# Reference
* [How do I show the value of a #define at compile-time?](https://stackoverflow.com/questions/1562074/)
