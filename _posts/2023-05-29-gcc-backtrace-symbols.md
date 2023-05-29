---
layout: post
title: "Backtrace Function Call in GCC"
author: "Borting"
categories: journal
tags: [C, Linux]
image: Rurikoin.jpg
---

在 debug kernel 的時候, 要追 function call stack 最簡單的方式就是呼叫 `dump_stack()`.
在 user space 寫 C function 時, gcc 也提供了一個類似的 API.

# Backtrace

Example code
```C
# example.c
#include <stdlib.h>

/* Obtain a backtrace and print it to stdout. */
void
print_trace (void)
{
  void *array[10];
  char **strings;
  int size, i;

  size = backtrace (array, 10);
  strings = backtrace_symbols (array, size);
  if (strings != NULL)
  {

    printf ("Obtained %d stack frames.\n", size);
    for (i = 0; i < size; i++)
      printf ("%s\n", strings[i]);
  }

  free (strings);
}

/* A dummy function to make the backtrace more interesting. */
void
dummy_function (void)
{
  print_trace ();
}

int
main (void)
{
  dummy_function ();
  return 0;
}
```

Compile 時, 要額外加 flag `-rdynamic`
```shell
gcc -rdynamic example.c
```

執行就可以看到 call stack 了
```
Obtained 5 stack frames.
./a.out(print_trace+0x2c) [0x560962f92215]
./a.out(dummy_function+0xd) [0x560962f922ae]
./a.out(main+0xd) [0x560962f922be]
/lib/x86_64-linux-gnu/libc.so.6(__libc_start_main+0xf3) [0x7f4da0870083]
./a.out(_start+0x2e) [0x560962f9212e]
```

# Reference

* [34.1 Backtraces](https://www.gnu.org/software/libc/manual/html_node/Backtraces.html)
