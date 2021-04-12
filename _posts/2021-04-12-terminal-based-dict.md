---
layout: post
title: "Terminal-based Offline Dictionary"
author: "Borting"
categories: journal
tags: [Linux]
image: Jokulsarlon.jpg
---

最近嫌查單字還要開 browser 或 app 查太麻煩了, 上網找了一個支援 terminal 的 offline 字典 -- [sdcv](https://dushistov.github.io/sdcv/).
紀錄一下安裝和使用.

# sdcv

sdcv 可以直接使用[星際譯王 (StarDict)](http://stardict-4.sourceforge.net/)的字典庫, 英漢字典的選擇和單字量是夠的.

## Installation

在 Ubuntu/Debian 上可以直接下載 package.

```bash
$ sudo apt install sdcv
```

## Dictionary Download

先在加目錄下建立放 dictionary 的資料夾.

```bash
$ mkdir -p $HOME/.stardict/dic
```

到星際譯王的[字典庫](http://download.huzheng.org/)下載需要的字典, 然後解壓縮到 `$HOME/.stardict/dic`.
我下載的是[朗道英漢字典](http://download.huzheng.org/zh_TW/stardict-langdao-ec-big5-2.4.2.tar.bz2).

```bash
$ wget http://download.huzheng.org/zh_TW/stardict-langdao-ec-big5-2.4.2.tar.bz2
$ tar jxf stardict-langdao-ec-big5-2.4.2.tar.bz2 -C $HOME/.stardict/dic
```

## Usage

輸入 `sdcv` 進入互動模式可連續查詢多個單字.
要離開的話, 輸入 `Ctrl-d`.

```bash
$ sdcv
```

單筆單字查詢

```bash
$ sdcv [vocabulary]
```

# Reference

- [完全用 GNU/Linux 工作 -- 文字界面的字典 sdcv](https://chusiang.gitbooks.io/working-on-gnu-linux/content/15.sdcv.html)
- [sdcv](https://dushistov.github.io/sdcv/)
