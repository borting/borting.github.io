---
layout: post
title: "Generate Stack Trace for C program"
author: "Borting"
categories: journal
tags: [C, Linux]
image: Rurikoin.jpg
---

# Generate Stack Trace
Crash 時會產生 SIGSEGV signal, 可以註冊 signal handler 來產生 back trace.

## Sample code

```C
#include <stdio.h>
#include <execinfo.h>
#include <signal.h>
#include <stdlib.h>
#include <unistd.h>

void segv_hdlr (int sig)
{
	void *array[64] = { NULL };
	size_t size = 0;
	char **strings = NULL;

	// Get void*'s for all entries on the stack
	// Provides symbol name for output
	size = backtrace(array, sizeof(array) / sizeof(void*));
	strings = backtrace_symbols(array, size);

	// Print out all the frames to stderr
	//backtrace_symbols_fd(array, size, STDERR_FILENO);
	fprintf(stderr, "\nGet fatal signal %d.\n", sig);
	fprintf(stderr, "Stack trace:\n");
	for (size_t i = 0; i < size; i++) {
		fprintf(stderr, "  %s\n", strings[i]);
	}

	exit(1);
}

void baz() {
	int *foo = (int*)-1;	// make a bad pointer
	printf("%d\n", *foo);	// causes segfault
}

void bar() { baz(); }

void foo() { bar(); }

int main(int argc, char **argv) {
	signal(SIGSEGV, segv_hdlr);	// install handler
	//sleep(5);

	// Call foo, bar, and baz.  baz segfaults.
	foo();
}

```

## Compilation

- `-g`: compile with debugging symbols to get human-readable function names and line numbers in the call trace.
- `-O0`: 不要最佳化, 避免部份 function call 被最佳化拿掉
- `-rdynamic`: makes symbols available for runtime dynamic linking

```shell
gcc -Wall -g -O0 -rdynamic crash.c
```

# Reference

* [How to automatically generate a stacktrace when my program crashes](https://stackoverflow.com/a/77336)
