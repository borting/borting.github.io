---
layout: post
title: "Difference between Docker Save and Export"
author: "Borting"
categories: journal
tags: [Docker, container]
image: chimei-museum.jpg
---

Docker 提供兩種輸出 image/container 成壓縮檔的方式: `save/load` 和 `export/import`
主要的差異是:
* `docker save` 將 docker image (包含 **parent layers**, tags, versions) 轉成壓縮檔, `docker load` 將還原所有的 layers 資訊
* `docker export` 將 docker container 的**檔案系統**(不包含 layers 資訊)轉成壓縮檔, `docker import` 後只會看到一個 layer.

# Docker Save and Load

使用 `docker save` 將 image 匯出成壓縮檔
```shell
$ docker save IMAGE:TAG docker_image.tar
$ docker save IMAGE:TAG | gzip > docker_image.tar.gz
```

若要保留所有 layer 的訊息
```shell
$ docker save IMAGE:TAG $(docker history -q IMAGE:TAG | tail -n +1 | grep -v \<missing\> | tr '\n' ' ') | gzip > docker_image.tar.gz
```

使用 `docker load` 將壓縮檔轉成 image
```shell
$ docker load < docker_image.tar
$ docker load < docker_image.tar.gz
$ gunzip -c docker_image.tar.gz | docker load
```

# Docker Export and Import

使用 `docker export` 將 container 匯出成壓縮檔
```shell
$ docker export CONTAINER_ID -o docker_container.tar
$ docker export CONTAINER_ID | gzip > docker_container.tar.gz
```

使用 `docker import` 將壓縮檔轉成 image
```shell
$ docker import /path/to/docker_container.tar NEW_IMAGE:NEW_TAG
$ docker import /path/to/docker_container.tar.gz NEW_IMAGE:NEW_TAG
```

## Import Images with New Configutations
因為存放在 docker layer 中的 ENV/CMD 資訊在 export 時不會被保存, 所以 `docker run` 上述指令產生的 image 會失敗出現以下訊息
```
docker: Error response from daemon: No command specified.
```

解決方法是在 import 把 ENV/CMD 資訊加回去.
以 container 執行後進入 shell 為例:
```shell
$ docker import --change 'CMD ["/bin/bash"]' /path/to/docker_container.tar NEW_IMAGE:NEW_TAG
```

## Flatten Docker Images

`docker export/import` 也常被用來移除 docker image 在建立的過程中累積在不同 layer 的更新/刪除檔案, 縮減 image 的大小.
方法如下:
```shell
$ docker run -d ORIG_IMAGE | tee >(xargs docker export | docker import --change 'CMD ["/bin/bash"]' - NEW_IMAGE > /dev/null) | xargs docker rm
```

# Reference
* [Difference between save and export in Docker](https://tuhrig.de/difference-between-save-and-export-in-docker/)
* [What is the difference between import and load in Docker?](https://stackoverflow.com/a/36932570)
* ["No command specified" from re-imported docker image/container](https://serverfault.com/a/797619)
* [Flattening Docker images](https://l10nn.medium.com/flattening-docker-images-bafb849912ff)
* [Can the output of one command be piped to two other commands?](https://superuser.com/a/7458)
* [Is there a way to tag a previous layer in a docker image or revert a commit?](https://stackoverflow.com/a/38236494)
* [使用 save / export 分享 image](https://ithelp.ithome.com.tw/m/articles/10249573)
* [把image另外儲存成檔案](https://peihsinsu.gitbooks.io/docker-note-book/content/docker-save-image.html)
