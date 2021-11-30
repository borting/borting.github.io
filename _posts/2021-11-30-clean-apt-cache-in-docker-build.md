---
layout: post
title: "Clean APT Cache during Docker Image Build"
author: "Borting"
categories: journal
tags: [Docker]
image: chimei-museum.jpg
---

以前就覺得 Docker image 很肥.
最近發現就算 Dockerfile 只有執行一行 `apt update` 也是會佔空間的!!!

舉個如下 Dockerfile 作為例子
```
FROM ubuntu:20.04
RUN apt update ;
```

```shell
$ docker build . -t temp

$ docker images
REPOSITORY                        TAG          IMAGE ID       CREATED          SIZE
temp                              latest       b105cc6751ec   5 minutes ago    105MB
ubuntu                            20.04        fb52e22af1b0   3 months ago     72.8MB

$ docker history temp:latest
IMAGE          CREATED              CREATED BY                                      SIZE      COMMENT
b105cc6751ec   About a minute ago   /bin/sh -c apt update ;                         31.9MB    
fb52e22af1b0   3 months ago         /bin/sh -c #(nop)  CMD ["bash"]                 0B        
<missing>      3 months ago         /bin/sh -c #(nop) ADD file:d2abf27fe2e8b0b5f…   72.8MB  
```

# Clean APT Cache

根據[這份官方文件](https://docs.docker.com/develop/develop-images/dockerfile_best-practices/)的說法, 原因是 apt update 後的 cache 佔了空間.
所以最好的作法是 `apt install` 完需要的 package 後, 把 APT cache 清掉.
```shell
$ rm -rf /var/lib/apt/lists/*
```

把上面的例子改成如下的 Dockerfile
```
FROM ubuntu:20.04
RUN apt update && rm -rf /var/lib/apt/lists/*
```

Docker build 出來後的 image 就可以發現 size 沒有增加了.
```shell
$ docker build . -t temp

$ docker images
REPOSITORY                        TAG          IMAGE ID       CREATED          SIZE
temp                              latest       afed09a38e9e   5 minutes ago    72.8MB
ubuntu                            20.04        fb52e22af1b0   3 months ago     72.8MB

$ docker history temp:latest
IMAGE          CREATED         CREATED BY                                      SIZE      COMMENT
afed09a38e9e   4 minutes ago   /bin/sh -c apt update  && rm -rf /var/lib/ap…   0B        
fb52e22af1b0   3 months ago    /bin/sh -c #(nop)  CMD ["bash"]                 0B        
<missing>      3 months ago    /bin/sh -c #(nop) ADD file:d2abf27fe2e8b0b5f…   72.8MB    
```

不看說明書就開始玩遊戲果然是個壞習慣 (掩面)
找個時間來把[這份官方文件](https://docs.docker.com/develop/develop-images/dockerfile_best-practices/)讀完.

# Reference
* [Best practices for writing Dockerfiles](https://docs.docker.com/develop/develop-images/dockerfile_best-practices/)
