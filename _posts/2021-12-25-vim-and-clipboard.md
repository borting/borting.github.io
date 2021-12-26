---
layout: post
title: "Copy and Paste between Vim and Clipboard"
author: "Borting"
categories: journal
tags: [Vim, Linux]
image: BayaoBay.jpg
---

工程師最常用的按鍵就是 `Ctrl-C` 和 `Ctrl-V` (誤).
為了快速地把從 Google / Stack Overflow 把找到的東西貼進 vim.
當然也要動點手腳讓 vim 可以存取系統的剪貼簿 (clipboard).

以下提供兩種 vim 存取 clipboard 的方法, 以及透過 SSH 連線到 remote server 操作 vim 時, 該如何存取 local desktop 的 clipboard.

# Vim and Clipboard

以下所說的 clipboard 指的是 system clipboard.
或更精確的說, 是 X11 的 `Ctrl+C clipboard` ([參考](https://www.uninformativ.de/blog/postings/2017-04-02/0/POSTING-en.html)).

## Use vim with clipboard feature

這個作法需要 vim 在編譯時開啟 `+clipboard` feature.
可透過以下指令查詢目前的 vim 是否有開啟此 feature
```shell
$ vim --version
```

若要在 Ubuntu 上安裝支援 `+clipboard` feature 的 vim, 可參考以下步驟
* Install vim compiled with clipboard feature
```shell
$ sudo apt install vim-gtk3
```

`.vimrc` 設定如下

* Configure `.vimrc` to copy/paste to/from clipboard
```
" Support <Ctrl-c> to copy selected text to system clipboard in visual mode
vnoremap <C-c> "+y

" Support <Ctrl-v> to paste content from system clipboard in visual mode
inoremap <C-v> <C-r>+

" Support <Ctrl-v> to paste yanked text in command-line mode
cnoremap <C-v> <C-r>+
```

* Configure `.vimrc` to store yanked text to clipboard.
(把 vim yank 的文字也存到 system clipboard)
```
set clipboard=unnamedplus
```

## Use vim with xclip

在 vim 不支援 clipboard feature 時可以搭配 `xclip` command 來和 system clipboard 互動.

* Install package
```shell
$ sudo apt install xclip
```

* Configure `.vimrc`
```
" Support <Ctrl-c> to copy selected text to system clipboard in visual mode
vmap <C-c> y:call system("xclip -i -selection clipboard", getreg("\""))<CR>

" Support <Ctrl-v> to paste content from system clipboard in visual mode
imap <C-v> <Esc>:call setreg("\"",system("xclip -o -selection clipboard"))<CR>p<insert>

" Support <Ctrl-v> to paste yanked text in command-line mode
cmap <C-v> <C-r>"
```

# Use Clipboard on Remote Server

在 remote server 操作 vim 時, 也可以在 ssh 時開啟 X11 forwarding 讓 remote server 可以存取 local desktop 的 clipboard.
(這會允許 remote server 拿到 local 的 X11 session 控制權, 所以**最好在可信任的 remote server 才做這件事**)

SSH 連線時開啟 X11 forwarding 和 X11 trstued forwarding:
```shell
$ ssh -XY remote_server
```

或是在 `.ssh/config` 的 remote server 加入 `ForwardX11 yes` 和 `ForwardX11Trusted yes`
```
Host remote_server
    HostName 192.168.1.68
    User borting
    Port 22
    PreferredAuthentications publickey
    IdentityFile ~/.ssh/id_rsa
    ForwardX11 yes
    ForwardX11Trusted yes
```

# Reference

* [How can I copy text to the system clipboard from Vim?](https://vi.stackexchange.com/a/96)
* [Accessing the system clipboard](https://vim.fandom.com/wiki/Accessing_the_system_clipboard)
* [In line copy and paste to system clipboard](https://vim.fandom.com/wiki/In_line_copy_and_paste_to_system_clipboard)
* [How to install Vim with +clipboard on Ubuntu 20.x](https://yattdeveloper.medium.com/how-to-install-vim-with-clipboard-on-ubuntu18-to-20-x-5cc4970eb3dc)
* [X11: How does "the" clipboard work?](https://www.uninformativ.de/blog/postings/2017-04-02/0/POSTING-en.html)
* [What's the difference between Primary Selection and Clipboard Buffer?](https://unix.stackexchange.com/a/139193)
* Mapping keys in Vim - Tutorial ([Part 1](https://vim.fandom.com/wiki/Mapping_keys_in_Vim_-_Tutorial_(Part_1)), [Part 2](https://vim.fandom.com/wiki/Mapping_keys_in_Vim_-_Tutorial_(Part_2)), [Part 3](https://vim.fandom.com/wiki/Mapping_keys_in_Vim_-_Tutorial_(Part_3)))
* [Vim document -- Key mapping, abbreviations and user-defined commands](https://vimhelp.org/map.txt.html#map.txt) ([簡中](https://yianwillis.github.io/vimcdoc/doc/map.html))
