---
layout: post
title: "Run Jenkins in Docker Container"
author: "Borting"
categories: journal
tags: [Jenkins, DevOps, Docker]
image: Wuling.jpg
---

因為工作需求, 在 Docker 中跑 Jenkins container 作為自己測試用.
這裡紀錄一下步驟.

# Run Jenkins Service

* Download [Jenkins image](https://hub.docker.com/r/jenkins/jenkins) from DockerHub.
```shell
docker pull jenkins/jenkins:lts-jdk11
```

* Create folder for jenkins container
```shell
mkdir -p $HOME/jenkins/jenkins_home
```

* Run Jenkins container
```shell
docker run \
	--name jenkins \
	-d --restart always \
	--net host \
	-v $HOME/jenkins/jenkins_home:/var/jenkins_home \
	jenkins/jenkins:lts-jdk11
```

* Access Jenkins Web from `http://127.0.0.1:8080/` and you need to enter password, which can be found at `$HOME/jenkins/jenkins_home/secrets/initialAdminPassword`.

* Install additional plugin
  * Go to "Manage Jenkins" --> "Manage Plugins"
  * Install "[Role-based Authorization Strategy](https://plugins.jenkins.io/role-strategy/)", "[Locale](https://plugins.jenkins.io/locale/)"

## Role-based Authotization

* Enable role-based authorization: go to "Manage Jenkins" --> "Configure Global Security" --> "Authorization" --> Check "Role-Based Strategy"
* Configure roles: go to "Manage Jenkins" --> "Manage and Assign Roles"

## Locale

* Go to "Manage Jenkins" --> "Configure System"
* Set "Default Language" to `zh_TW`

# Role Management

## General
* '真' 管理員要有 "Overall --> "
* User 登入要能夠看到東西, 所屬的 roles 只少要有 "Overall --> Read" 權限.

## Node 管理權限
* 要管理 Jenkins nodes, user 所屬的 roles 需要有 "Agent" 權限.
  * 有 "Agent --> Create" 權限才可以在 "Dashboard --> Node" 下 "New Node"
  * 有 "Agent --> Connect/Disconnect" 權限才可以將 Jenkins nodes 連/斷線
* Agent 下的



# Jenkins Runtime Unit

* Node 和 agent (以前稱 slave) 都是 Jenkins 來執行 jobs 的實體 (server, etc.).
  * Agent is for declarative pipelines
  * Node is for scripted pipelines


# Reference

# General
* [探索 Jenkins-CI 從認識到應用](https://ithelp.ithome.com.tw/users/20091802/ironman/925)

## Setup
* [Official Jenkins Docker image on GitHub](https://github.com/jenkinsci/docker)
* [使用 Docker 安裝 Jenkins](https://twblog.hongjianching.com/2018/10/09/install-jenkins-with-docker/)
* [Jenkins not able to access internet when running as docker container](https://stackoverflow.com/q/39709941)


## Role-based Strategy
* [Role-based Authorization Strategy](https://plugins.jenkins.io/role-strategy/)
* [Jenkins - Role Based Strategy Setup](https://www.c-sharpcorner.com/article/jenkins-role-based-strategy-setup/)

