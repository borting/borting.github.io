---
layout: post
title: "Add new user on Ubuntu Server"
author: "Borting"
categories: journal
tags: [Linux]
image: chimei-museum.jpg
---

# Change the Default Home Directory

Edit /etc/adduser.conf

```text
DHOME=/home2
```

Then, add new user

```shell
sudo adduser NEW_USER
```

# Add New User to group

Add user to sudo group

```shell
sudo usermod -aG sudo NEW_USER
```

Add user to docker group

```shell
sudo usermod -aG docker NEW_USER
```

# Hide New User from Login Screen

Edit /var/lib/AccountsService/users/NEW\_User

```text
[User]
SystemAccount=true
```

Then, restart account service and display manager

```shell
# Restart account service
sudo service accounts-daemon restart

# For GDM (default in modern Ubuntu)
sudo systemctl restart gdm3.service

# For LightDM (used in some Ubuntu flavors)
sudo systemctl restart lightdm.service
```

# Reference

* [How to hide users from the GDM login screen?](https://askubuntu.com/a/545764)
