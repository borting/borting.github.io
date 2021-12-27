---
layout: post
title: "Use Vim to Diff Two Directories"
author: "Borting"
categories: journal
tags: [Vim, Git, Bash, Linux]
image: BayaoBay.jpg
---

Command line 用習慣了, 所以想在 Vim 上做出像是 [Meld](https://gitlab.gnome.org/GNOME/meld) 一樣的 directory diff 功能.
基本上只要裝一個 [dirdiff](https://github.com/will133/vim-dirdiff) vim plugin, 就可以做到囉.

# Install Dirdiff Vim Plugin

* Install using vim-plug
```
Plug 'will133/vim-dirdiff'
```

# Provide Dirdiff as a Bash Command

最簡單的應用就是把 vimdiff + dirdiff 包成一個 command

* Configure `~/.bashrc`
```
# Use vimdiff + DirDiff plugin to diff files in diretory
function dirdiff() {
	# Check input parameters are valid
	local execFlag=1
	if [ $# -ne 2 ]; then
		execFlag=0
	elif ! test -d "${1}"; then
		execFlag=0
		echo "Error: dir1 is not a directly"
	elif ! test -d "${2}"; then
		execFlag=0
		echo "Error: dir2 is not a directly"
	fi

	if [ $execFlag == 1 ]; then
		vim -c "set diffopt+=iwhite" -c "DirDiff ${1} ${2}"
	else
		echo "usage: dirdiff dir1 dir2"
	fi
}

# Export dirdiff function as a bash command
export -f dirdiff
```

* Usage
```shell
$ dirdiff [dir_1] [dir_2]
```

# Use Dirdiff as Git Diff Tool

另一個進階的應用是用 Dirdiff 取代預設的 git diff tool

* Configure `~/.gitconfig`
```
[diff]
	tool = vimdirdiff

[difftool "vimdirdiff"]
	cmd = vim -c \"set diffopt+=iwhite\" -c \"DirDiff $LOCAL $REMOTE\"

[difftool]
	prompt = false
```

* Usage
```shell
$ git difftool --dir-dif <commitish_1> <commitish_2>
```

# Reference

* [dirdiff](https://github.com/will133/vim-dirdiff)
* [How to diff and merge two directories?](https://vi.stackexchange.com/a/790)
