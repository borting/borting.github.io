---
layout: post
title: "Vim C Indention"
author: "Borting"
categories: journal
tags: [BayaoBay.jpg]
image: cards.jpg
---

# Customize C Indenting Style

```shell
set cinoptions+=(2s,:0s,t0
```

`(2s`: indent 2 shiftwidth from the line with the unclosed parenthesis

```C
if (c1 && (c2 ||
		c3))
    foo;
```C
```
if (c1 &&
	    (c2 || c3))
```

`:0s`: Place case labels 0 characters from the indent of the switch()

```C
switch(x)
{
case 1:
	a = b;
default:
	c = a
}
```

`t0`: Indent a function return type declaration 0 characters from the margin

```C
int
func()
```

# Reference

* [indent.txt](https://vimhelp.org/indent.txt.html)
