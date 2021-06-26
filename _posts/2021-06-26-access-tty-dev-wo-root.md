---
layout: post
title: "Access TTY Device on Linux"
author: "Borting"
categories: journal
tags: [Linux]
image: Jokulsarlon.jpg
---

紀錄一下如何讓一般 user 打開 serial port, 並且用 `screen` 指令操作 consile.

# Allow Access to TTY without Root Permission

* 把 user 加到 `dialout` (Ubuntu) 或 `tty` group.
```
$ sudo adduser USER dialout
$ sudo usermod -a -G tty USER
```

# Access Serial Drvice

* 使用 `screen` 指令開啟 serial port
```
$ screen /dev/ttySUSB0 115200,cs8
```

* 離開按 `Ctrl-a + k`

* 更多 screen 操作參考[這裡](https://kb.iu.edu/d/acuy).

# Refrence

* [How do I allow a non-default user to use serial device ttyUSB0?](https://askubuntu.com/a/112572)
* [Cannot open /dev/ttyUSB0: Permission denied](https://github.com/esp8266/source-code-examples/issues/26#issuecomment-320999460)
* [5 Linux / Unix Commands For Connecting To The Serial Console](https://www.cyberciti.biz/hardware/5-linux-unix-commands-for-connecting-to-the-serial-console/)
* [About the screen program in Unix](https://kb.iu.edu/d/acuy)
