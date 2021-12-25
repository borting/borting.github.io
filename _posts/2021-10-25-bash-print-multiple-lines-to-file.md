---
layout: post
title: "Print Multiple Lines to File from Bash"
author: "Borting"
categories: journal
tags: [bash, Linux]
image: shell.jpg
---

Print multiple lines to file from bash.
```bash
cat << EOF >> file.txt
line 1
line 2
EOF
```

# Reference
* [How To Cat EOF For Multi-Line String In Linux Bash?](https://linuxtect.com/how-to-cat-eof-for-multi-line-string-in-linux-bash/)
