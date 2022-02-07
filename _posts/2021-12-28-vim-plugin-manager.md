---
layout: post
title: "Use vim-plug to Management Vim Plugins"
author: "Borting"
categories: journal
tags: [Vim]
image: BayaoBay.jpg
---

以前都用 [Vundle](https://github.com/VundleVim/Vundle.vim) 來管理 vim 的 plugins.
最近看了這一篇[文章](https://junegunn.kr/2014/07/vim-plugins-and-startup-time/)比較各種 vim plugin manager 的啟動速度後, 就決定改用 [vim-plug](https://github.com/junegunn/vim-plug) 啦.

vim-plug 和 Vundle 相比有幾個優點:
1. 啟動速度快
2. 可以 on-demand 載入 plugin, 更縮短了啟動時間
3. 可以將 vim-plug 安裝指令寫在 `.vimrc` 內, 到一個新的環境時只要複製 `.vimrc` 再開啟 Vim 就會自動安裝 vim-plug 與所有的 plugins

# Vim-plug Installation

* Install via command line
```shell
curl -fLo ~/.vim/autoload/plug.vim --create-dirs https://raw.githubusercontent.com/junegunn/vim-plug/master/plug.vim
```

* Or, add the following lines to `.vimrc` then restart Vim.
The vim-plug will be installed automatically.
```
if empty(glob('~/.vim/autoload/plug.vim'))
    silent !curl -fLo ~/.vim/autoload/plug.vim --create-dirs
        \ https://raw.githubusercontent.com/junegunn/vim-plug/master/plug.vim
endif
```

* 若之後要 upgrade vim-plug, 可以在 Vim 內輸入
```
:PlugUpgrade
```

# Plugin Management

## Basic Management

* 所有透過 vim-plug 管理的 plugins 都要放在 `~/.vimrc` 的特定宣告範圍內.
```vim
" Begin of plugin list
call plug#begin()

" Plugin List
" ...
" ...

" End of plugin list
call plug#end()
```

* Plugin 的宣告方式與 `Vundle` 相似.
Default 從 github 抓
```vim
" vim-dirdiff
Plug 'will133/vim-dirdiff'
```
也支援任何形式的 git URL address
```vim
" vim-dirdiff
Plug 'https://github.com/will133/vim-dirdiff.git'
```
或是 local host 的位址
```vim
Plug '~/repos/vim-dirdiff'
```

* 指定 plugin 的版本
```vim
Plug 'will133/vim-dirdiff', { 'branch': 'master' }
```
```vim
Plug 'will133/vim-dirdiff', { 'tag': '1.1.7' }
```
```vim
Plug 'will133/vim-dirdiff', { 'commit': '84bc8999fde4b3c2d8b228b560278ab30c7ea4c9' }
```

* 如果 plugin 下載後需要額外編譯, e.g. `[YouCompleteMe](https://github.com/ycm-core/YouCompleteMe)`, 可以加入 post-update hook
```vim
Plug 'ycm-core/YouCompleteMe', { 'do': './install.py' }
```

* 安裝 plugin, 預設放在 `~/.vim/plugged`
```
:PlugInstall
```

* 更新 plugin
```vim
:PlugInstall
```

* Check plugin status
```vim
:PlugStatus
```

* 移除 plugin 前先把 plugin 從 list 中移除, 然後
```vim
:PlugClean!
```

## On-demand Loading

* On-demand loading for specific file type using `for` option.
以下面設定為例, 在開啟 C or CPP 檔案時, 才會 load `tagbar` plugin.
```vim
Plug 'borting/unmaze.vim', { 'for': ['c', 'cpp'] }
```

* On-demand loading with toggle command using `on` option.
以下面設定為例, 在按下 `<F8>` 後才會 load `tagbar` plugin.
```vim
call plug#begin('~/.vim/plugged')
Plug 'majutsushi/tagbar', { 'on': 'TagbarToggle' }
call plug#end()

nmap <F8> :TagbarToggle<CR>
```

## Automatically Installation



# Misc

在 Vim 的 command line mode 中可以下指令 reload `~/.vimrc`, 不用每次修改完都要先離開 Vim 再開啟才會生效
```vim
:source ~/.vimrc
```

# Reference

* [vim-plug](https://github.com/junegunn/vim-plug)
* [Vim plugins and startup time](https://junegunn.kr/2014/07/vim-plugins-and-startup-time/)
* [Reload .vimrc in Vim without restart](https://superuser.com/a/286987)
