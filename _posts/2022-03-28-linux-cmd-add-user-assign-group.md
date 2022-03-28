---
layout: post
title: "Create Linux User and Assign Group via Command Line"
author: "Borting"
categories: journal
tags: [Linux]
image: Jokulsarlon.jpg
---

紀錄一下如何透過 command line 建立 linux user

# Create User

建立 user 和 home dir
```shell
sudo useradd -s /bin/bash -m USER_NAME
```

若要指定特殊的 home dir
```shell
sudo useradd -m -d USER_HOME USER_NAME
```

若一開始建立 user 忘了加 `-m` (`--create-home`), 可透過其他指令建立
```shell
sudo mkhomedir_helper USER_NAME
```

更改 login shell to bash
```shell
sudo usermod --shell /bin/bash USER_NAME
```

# Add User to Group

* Create a Group
```shell
sudo groupadd GROUP_NAME
```

* Add an existing user to a group
```shell
sudo usermod -a -G GROUP_NAME USER_NAME
```

# Reference

* [How to Create Users in Linux (useradd Command)](https://linuxize.com/post/how-to-create-users-in-linux-using-the-useradd-command/)
* [How to Create Home Directory for Existing User in Linux](https://linoxide.com/create-home-directory-existing-user-linux/)
* [3 Ways to Change a Users Default Shell in Linux](https://www.tecmint.com/change-a-users-default-shell-in-linux/)
* [How to Add User to Group in Linux](https://linuxize.com/post/how-to-add-user-to-group-in-linux/)
