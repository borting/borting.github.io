---
layout: post
title: "GCC Warnings"
author: "Borting"
categories: journal
tags: [C,CPP]
image: Rurikoin.jpg
---

Many projects enable additional diagnostics by using the `-Wall` and `-Wextra` command-line options. 
Some projects even turn them into errors via `-Werror` as their first line of defense.

# Front-end Warnings

* Preprocessor warnings
  * `-Wunused-macros`
* Lexical warnings
  * `-Wempty-body` detects if or for statements with no body
  * `-Wsizeof-array-argument` detects applying the sizeof operator to a function parameter declared using the array
  * `-Wmissing-parameter-type` detects declaring function parameters without specifying their type
* Type-safety warnings
  * `-Wint-to-pointer-cast` and `-Wpointer-to-int-cast`
  * `-Wsign-compare` detects comparisons between expressions with different signedness.
  * `-Wcast-qual`
    * warns whenever a pointer is cast so as to remove a type qualifier from the target type. For example, warn if a const char \* is cast to an ordinary char \*.
    * warns when making a cast that introduces a type qualifier in an unsafe way. For example, casting char \*\* to const char \*\* is unsafe
  * `-Wcast-align` warns whenever a pointer is cast such that the required alignment of the target is increased.
    For example, warn if a char * is cast to an int * on machines where integers can only be accessed at two- or four-byte boundaries.
  * `-Wcast-function-type` warns when a function pointer is cast to an incompatible function pointer.
     The function type void (*) (void) is special and matches everything, which can be used to suppress this warning.
* Others
  * `-Wunused-value`, `-Wunused-result`, and `-Wunused-function`
  * `-Wshift-count-negative`, `-Wshift-count-overflow`, `-Wshift-negative-value`, and `-Wshift-overflow`

# Middle-end Warnings

Also called flow-based warnings.

# Reference
* [Understanding GCC warnings](https://developers.redhat.com/blog/2019/03/13/understanding-gcc-warnings)
* [Understanding GCC warnings, Part 2](https://developers.redhat.com/blog/2019/03/13/understanding-gcc-warnings-part-2)
* [GCC Online Doc Sec. 3.8 Options to Request or Suppress Warnings](https://gcc.gnu.org/onlinedocs/gcc/Warning-Options.html)
* [GCC Online Doc Sec. 14.9 Warning Messages and Error Messages](https://gcc.gnu.org/onlinedocs/gcc/Warnings-and-Errors.html#Warnings-and-Errors)
