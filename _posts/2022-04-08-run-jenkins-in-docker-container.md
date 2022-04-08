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

* Use JNLP to control agents.
Go to `Dashboad` --> "Configure Global Security" --> set `TCP port for inbound agents` to `50000` --> in `agent protcol`, check `Inbound TCP Agent Protocol/4 (TLS encryption)`



# Install Plugins

Install plugins:
* Go to "Manage Jenkins" --> "Manage Plugins"
* Install "[Role-based Authorization Strategy](https://plugins.jenkins.io/role-strategy/)", "[Locale](https://plugins.jenkins.io/locale/)", "[Gerrit Trigger](https://plugins.jenkins.io/gerrit-trigger/)"

## Role-based Authotization

* Enable role-based authorization: go to "Manage Jenkins" --> "Configure Global Security" --> "Authorization" --> Check "Role-Based Strategy"
* Configure roles: go to "Manage Jenkins" --> "Manage and Assign Roles"

## Locale

* Go to "Manage Jenkins" --> "Configure System"
* Set "Default Language" to `zh_TW`













# Role Management

Go to "Manage Jenkins" --> "Manage and Assign Roles" for configuration

## General

Global roles:
* 有 "Overall --> Administer" 權限的 role 才能 configure/manage roles 和 assign roles to users.
* Role 至少要有 "Overall --> Read" 的權限, 登入後才能夠看到東西
* Agent 相關的 role permission 是用來管理 Jenkins 的 runtime unit (Slave/Node/Agent) 的, 屬於 build resources 管理
* Job 相關的 role permission 是用來管理 build 的, 屬於 build configuration 管理

## Node 管理權限
* 要管理 Jenkins nodes, user 所屬的 roles 需要有 "Agent" 權限.
  * 有 "Agent --> Create" 權限才可以在 "Dashboard --> Node" 下 "New Node"
  * 有 "Agent --> Connect/Disconnect" 權限才可以將 Jenkins nodes 連/斷線
* 可以用 `Label` 去管理可執行相同工作的 nodes

## 分工

若從管理的角度分工, role 可分為三個類型:
1. `admin`: 管理 role 建立和 user 管理, 屬於部門主管的工作
2. `manager`: (1) 管理 Jenkins controller 與 Jenkins Agent 連接, 和 (2) 定義可以一般使用者觸發的 jobs 和 daily build.
此 role 需要有 "Agent" 和 "Job" 的權限.
3. `user`: 一般的使用者, 只能觸發定義好的 job.
此 role 只需有部份的 "Job" 權限.

Jenkins Controller 的實際管理者需要有 `manager` 的權限

## TODO
* Study how to use "Item roles" and "Node roles"





# Job

## What is Job

Type:
* Free-Style Job
* Multi-Configuration Job
* Pipeline (Scripted Pipeline & Declarative Pipeline)
* Multibranch Pipeline

## Add a Job for Github

* Go to `http://JENKINS_IP:PORT/view/all/newJob`, enter job name and choose `Freestyle projecta`
* Setup `Discard old builds`

* Check `Execute concurrent builds if necessary` 允許同時有多個 executor 執行 build queue 裡的 task, 預設 build queue 裡的 task 是一次只執行一個
* Check `Restrict where this project can be run` 用 Label 設定可以被哪些 agent group 執行.
* Check `Source Code Management` --> `Git`
  * `Repository URL`: add url for ssh clone
  * `Credentials`: choose a crendential of which public key has been uploaded to Github project.















# Agent

假設一個擁有 `Agent` 和 `Job` 所有權限的管理者, 這裡說明他可以對 Jenkins Controller 的操作.

## Jenkins Agent Settings

* Install Java for JNLP ([Java Network Launch Protocol](https://en.wikipedia.org/wiki/Java_Web_Start)) communication between controller and agent.
```shell
# On ubuntu 20.04
sudo apt install openjdk-17-jre
```

* The Jenkins jobs dispatched to this node will be executed by the account that invokes the Jenkins slave agent.
Hence, we need to add the acount which would execte Jenkins jobs to `docker` group.

* Create workspace
```shell
mkdir -p $HOME/workspace
mkdir -p $HOME/jenkins/scripts
```

## Agent Configuration

Create new node on controller
* Go to `Dashboard` --> `Set up an agent` or go to `Dashboard` --> `Build Executor Status` --> `New Node`
* Set node name and check `Permanent Agent`
* Set `Number of executors` to define max number of concurrent builds
* Create a working dir for jenkins and set an absolute path to `Remote root directory`
```shell
mkdir -p $HOME/workspace
```
* Set a `label` to create agent group for same jobs
* Set `Launch method` as `Launch agent by connecting it to the controller`

After node createtion, 
* Go to `Dashboard` --> `Build Executor Status` --> click the icon of the newly added node.
Or visit node management page: `http://JENKINS_IP:PORT/computer/`
* Download `agent.jar`, upload it to the Jenkins node, and put it under `$HOME/jenkins/scripts`
* Run the command specified in the web, for example
```shell
java -jar agent.jar -jnlpUrl http://172.21.35.148:8080/computer/Archer/jenkins-agent.jnlp -secret 6af8b1c40bbec4fa0beb682c5340ba5fa243af4ab50c1dbcdb7c32d10fe93ee5 -workDir "/home/jenkins/workspace"
```
* Setup `.gitconfig`, `.ssh/config`, `.ssh/id_rsa_jenkins.pub`





# Credential

## Configure Credential for GitHub

* Create ssh keys
```shell
ssh-keygen -t rsa
```

* 把產生的 public key 放到 github project 的 "repository settings" --> "Deploy keys" --> "Add deploy key"

* Go to `http://JENKINS_IP:PORT/credentials/store/system/domain/_/`, 將 private key 新增到 Jenkins
  * `Kind` select `SSH username with private key`
  * `Scope` select `Global (Jenkins, node, ...)`
  * `ID` give any name for key management
  * `Username` add user name for added ssh credential
  * Check `Private Key` and add private key generated in previous step








# Jenkins Runtime Unit

* Node 和 agent (以前稱 slave) 都是 Jenkins 來執行 jobs 的實體 (server, etc.).
  * Agent is for declarative pipelines
  * Node is for scripted pipelines

* Jenkins server will dispatch job to these node automatically according to the job configuration and node labels.

## Agent 和 Node 差異

* 參考 [Jenkins Glossary](https://www.jenkins.io/doc/book/glossary/)
  * Agent: An agent is typically a machine, or container, which connects to a Jenkins controller and executes tasks when directed by the controller.
  * Node: A machine which is part of the Jenkins environment and capable of executing Pipelines or Projects. Both the Controller and Agents are considered to be Nodes.

* Agent 是可執行 job/pipeline 的單位, 可以在實體機器上或是 VM 上.
* Agent 是 cloud 上的 VM 的話就可以被 dynamic provisioning and allocation
* Label 管理的是 Agent 而非 Node

* Node 和 Agent 最大的差異就是 Node 除了 agents 外, 包含了 controller.
但多數時候, controller 不執行 job/pipeline.
所以 Node 和 Agent 兩個詞基本上可以互換.









# Misc

## Distributed Builds

`Distributed Builds` concepts require builds be executed on other nodes than the built-in node to ensure the stability of the Jenkins controller.
* To disable build on Jenkins controller, go to "Manage Jenkins" --> "Manage Nodes and Clouds" --> choose "Build-in Node" --> click "Configure" icon --> set "Number of executors" to "0".
* [Controller Isolation](https://www.jenkins.io/doc/book/security/controller-isolation/)

## Agent to Controller Access Control

* [Customizing Agent →  Controller Security](https://www.jenkins.io/doc/book/security/controller-isolation/agent-to-controller/)

## Jenkins 名詞

* Jenkins 的許多名詞有經過 BLM 處理.
例如 Jenkins master/slave, 轉成叫中性的 Jenkins controller 和 Jenkins node/agent.
在看文件時可以注意.

## Git Checkout

* 不 checkout 的方法, 使用 `skipDefaultCheckout`
  * [Set a Jenkins job to not to clone the repo in SCM](https://devops.stackexchange.com/a/1074)
  * [Pipeline Syntax](https://www.jenkins.io/doc/book/pipeline/syntax/)













# Reference

## General
* [探索 Jenkins-CI 從認識到應用](https://ithelp.ithome.com.tw/users/20091802/ironman/925)

## Setup
* [Official Jenkins Docker image on GitHub](https://github.com/jenkinsci/docker)
* [使用 Docker 安裝 Jenkins](https://twblog.hongjianching.com/2018/10/09/install-jenkins-with-docker/)
* [Jenkins not able to access internet when running as docker container](https://stackoverflow.com/q/39709941)
* [There is no "Launch agent via Java Web Start" option in my jenkins when I adding a windows slave node](https://stackoverflow.com/a/58024425)

## Role-based Strategy
* [Role-based Authorization Strategy](https://plugins.jenkins.io/role-strategy/)
* [Jenkins - Role Based Strategy Setup](https://www.c-sharpcorner.com/article/jenkins-role-based-strategy-setup/)
* [保密防諜 - Jenkins 簡而易懂的人員管理](https://ithelp.ithome.com.tw/articles/10157951)

## Agent Management

* [Jenkins : Distributed builds](https://wiki.jenkins.io/display/JENKINS/Distributed+builds)

## Credential

* [SSH authentication between GitHub and Jenkins]()https://medium.com/appgambit/d873dd138db0

## Github

* [How to Integrate Your GitHub Repository to Your Jenkins Project](https://www.blazemeter.com/blog/how-to-integrate-your-github-repository-to-your-jenkins-project)

# Question

* What is locable resource?
* What is the purpose of SCM permission?
* What is the difference b/w Node roles and Item roles?


