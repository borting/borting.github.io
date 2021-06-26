---
layout: post
title: "SSH Login using Hostname and Login without Password"
author: "Borting"
categories: journal
tags: [Linux]
image: Jokulsarlon.jpg
---

每次 login server 都要打一長串帳號 + IP + port + 密碼打到有點煩.
紀錄一下偷懶的方法: (1) 免打密碼, (2) 使用 hostname 代替 IP address

# Login without Password

要準備一組公鑰上傳到 server, 然後用私鑰登入.

* Generate SSH key.
The key is generated under `~/.ssh/`: `id_rsa` (private key) and `id_rsa.pub` (public key).
```bash
# Create rsa key with 4096 bits
$ ssh-keygen -t rsa -b 4096
```

* Create a folder on server to store ssh key
```bash
$ ssh user@server.ip.addr -C "mkdir -p ~/.ssh"
```

* Upload the public key to server
```bash
$ cat .ssh/id_rsa.pub | ssh user@server.ip.addr "cat >> .ssh/authorized_keys"
```

* Then, you do not need to enter password starting from next login.

# Login using Hostname

在 `.ssh/config` 加入 server 的資訊, 範例
```
Host myserver
	HostName 192.168.1.6
	User borting
	Port 22
	PreferredAuthentications publickey
	IdentityFile ~/.ssh/id_rsa
```

# Reference

* [SSH login without password](http://www.linuxproblem.org/art_9.html)
* [OpenSSH Config File Examples For Linux / Unix Users](https://www.cyberciti.biz/faq/create-ssh-config-file-on-linux-unix/)
* [Using the SSH Config File](https://linuxize.com/post/using-the-ssh-config-file/)
