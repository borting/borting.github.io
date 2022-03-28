---
layout: post
title: "Run Jenkins in Docker Container"
author: "Borting"
categories: journal
tags: [Jenkins, DevOps, Docker]
image: Wuling.jpg
---

因為工作需求, 用 Docker container 建了一個 Jenkins service 作為自己測試 Jenkins 功能的 test server.
這裡紀錄一下步驟.

# Setup Jenkins Service on Docker Container

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

* Access Jenkins Web from `http://127.0.0.1:8080/`

* At first login, you need to enter password, which can be found at `$HOME/jenkins/jenkins_home/secrets/initialAdminPassword`.

# Plugins

Install additional plugin
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

Global roles:
* 有 "Overall --> Administer" 權限的 Role 才有權限管理 roles 和 users.
* 所有 role 燈至少要有 "Overall --> Read" 的權限, 登入後才能夠看到東西

## Node 管理權限
* 要管理 Jenkins nodes, user 所屬的 roles 需要有 "Agent" 權限.
  * 有 "Agent --> Create" 權限才可以在 "Dashboard --> Node" 下 "New Node"
  * 有 "Agent --> Connect/Disconnect" 權限才可以將 Jenkins nodes 連/斷線
* Agent 下的



# Jenkins Runtime Unit

* Node 和 agent (以前稱 slave) 都是 Jenkins 來執行 jobs 的實體 (server, etc.).
  * Agent is for declarative pipelines
  * Node is for scripted pipelines


# Misc

## Distributed Builds

`Distributed Builds` concepts require builds be executed on other nodes than the built-in node to ensure the stability of the Jenkins controller.
* To disable build on Jenkins controller, go to "Manage Jenkins" --> "Manage Nodes and Clouds" --> choose "Build-in Node" --> click "Configure" icon --> set "Number of executors" to "0".
* [Controller Isolation](https://www.jenkins.io/doc/book/security/controller-isolation/)

## Agent to Controller Access Control

* [Customizing Agent →  Controller Security](https://www.jenkins.io/doc/book/security/controller-isolation/agent-to-controller/)


# Reference

## General
* [探索 Jenkins-CI 從認識到應用](https://ithelp.ithome.com.tw/users/20091802/ironman/925)

## Setup
* [Official Jenkins Docker image on GitHub](https://github.com/jenkinsci/docker)
* [使用 Docker 安裝 Jenkins](https://twblog.hongjianching.com/2018/10/09/install-jenkins-with-docker/)
* [Jenkins not able to access internet when running as docker container](https://stackoverflow.com/q/39709941)


## Role-based Strategy
* [Role-based Authorization Strategy](https://plugins.jenkins.io/role-strategy/)
* [Jenkins - Role Based Strategy Setup](https://www.c-sharpcorner.com/article/jenkins-role-based-strategy-setup/)


# Question

* What is locable resource?
* What is the purpose of SCM permission?
* What is the difference b/w Node roles and Item roles?


