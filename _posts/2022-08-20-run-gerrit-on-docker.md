---
layout: post
title: "Run Gerrit Server on Docker Container"
author: "Borting"
categories: journal
tags: [Git, Gerrit, Docker]
image: plum.jpg
---

因為想摸索一下 [git-repo](https://gerrit.googlesource.com/git-repo/), 所以用 docker 快速架了一個 Gerrit server.

# Setup

Get gerrit docker image
```shell
$ docker pull gerritcodereview/gerrit
```

Create folders to store gerrit data outside of container.
```shell
$ mkdir cache-volume db-volume git-volume index-volume
$ chmod 777 cache-volume db-volume git-volume index-volume
```

Create a yaml file for docker compose
```yaml
version: '3'

services:
  gerrit:
    image: gerritcodereview/gerrit
    container_name: gerrit
    volumes:
       - /path/to/gerrit_storage/git-volume:/var/gerrit/git
       - /path/to/gerrit_storage/db-volume:/var/gerrit/db
       - /path/to/gerrit_storage/index-volume:/var/gerrit/index
       - /path/to/gerrit_storage/cache-volume:/var/gerrit/cache
    ports:
       - "29418:29418"
       - "8080:8080
```

Start gerrit docker container
```shell
$ cd /path/to/gerrit_storage/
$ docker-compose up
```

# Reference

* [Gerrit Code Review docker image](https://gerrit.googlesource.com/docker-gerrit/)
* [Understanding how uid and gid work in Docker containers](https://medium.com/@mccode/c37a01d01cf)
* [Isolate containers with a user namespace](https://docs.docker.com/engine/security/userns-remap/)
