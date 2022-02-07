---
layout: post
title: "Use Multi-stage Builds to Generate Smaller Docker Images"
author: "Borting"
categories: journal
tags: [Docker, container]
image: chimei-museum.jpg
---

縮減 image size 除了使用之前提到的 [docker export/import]({% post_url 2022-02-07-docker-save-vs-export %}) 方法外, 另一個作法就是使用 docker 在 v17.05 開始提供的 multi-stage builds 來建制 docker image.

# History

在 multi-stage builds 推出以前的作法是使用兩個 Dockerfile: 一個負責產生編譯 binary 的 builder image, 另一個產生實際執行的 main image
builder image 產生的 binary 可以透過 host 的 disk 空間傳遞, 或是透過 pipe 將 binary 與 main image 的 Dockerfile 傳給 docker build 執行.
如此, 編譯 binary 產生的中繼檔就只會存在 builder image 中.
詳細作法可以參考[這篇文章](https://github.com/kevingo/Go-Small-Docker-Image).

# Multi-stage Builds

Multi-stage builds 簡單來說就是把上述兩個 Dockerfile 合併成一個, 並在 Dockerfile 中利用 `COPY` 指令從 builder image 複製 binary 到 main image 中.
* Dockerfile 裡會有多個 `FROM` 關鍵字, 每一個 `FROM` 代表一個 build stage, 產生一個 builder image.
* 一個 Dockerfile 可以有多個 build stage, 並從 0 開始編號.
也可以在 `FROM ... ` 後面加入 `AS` 關鍵字, 給 build stage 一個 alias name.
* Dockerfile 在描述 builder image 的建立方法和之前相同.
* Dockerfile 在描述 main image 的建立時, 可以用 `COPY --from=` 命令指定從哪個 build stage 產生的 builder image 拷貝檔案.
`--from-` 後可以使用 build stage 編號或是 alias name.
* 一個已建立的 builder image 可作為後建立 image 的 base

一個簡易的 multiple-stage build 的 Dockerfile 範例如下:
* `repo_downloader` 和 `main` image 都使用 `common_base_image` 作為 base image
* `repo_downloader` 負責 clone `git-repo` project, 並 checkout v2.19 版
* 在 Main stage 將 `repo_donloader` image 中 checkout 出的 v2.19 repo 複製到 main image 中

```Dockerfile
# Builder Stage

FROM ubuntu:20.04 AS common_base_image

RUN apt update; \
    apt install -y git python3; \
    update-alternatives --install /usr/bin/python python /usr/bin/python3 1; \
    rm -rf /var/lib/apt/lists/* && apt clean && apt autoclean; 

# Builder Stage

FROM common_base_image AS repo_downloader
ARG BUILD_ID
LABEL stage=builder
LABEL build=$BUILD_ID

RUN cd /root/; git clone https://gerrit.googlesource.com/git-repo; \
    cd /root/git-repo; git checkout v2.19;

# Main Stage

FROM common_base_image

COPY --from=repo_downloader /root/git-repo/repo /usr/bin/repo
```

執行 multi-stage build 就下:
```shell
$ docker build . -t IMAGE:TAG
```

## Remove Builder Image

Multi-stage 編譯產生的 builder images 會被保留下來, 需要手動刪除.
若要一行指令搞定的話, 可以利用 Dockerfile 裡的 `ARG` 和 `LABEL` 命令.

以上面的範例來說, 在要刪除 `repo_downloader` image 的 build step 加入下面這三行
```Dockerfile
ARG BUILD_ID
LABEL stage=builder
LABEL build=$BUILD_ID
```

然後在編譯時, 改成用以下指令
* 先產生一組亂數作為環境變數 `BUILD_ID`
* 將 `$BUILD_ID` 作為 `docker build 的參數傳入`
* Dockerfile 在編譯 builder image 時將參數轉成 LABEL
* Multi-stage build 結束後, 透過 filter 設定 label 刪除 builder images
```shell
$ (BUILD_ID=`echo $RANDOM | md5sum | head -c 20`; \
    docker build --build-arg BUILD_ID=$BUILD_ID . -t multistage && echo $BUILD_ID && \
    yes 'y' | docker image prune --filter label=stage=builder --filter label=build=$BUILD_ID)
```

## Stop Build at Specific Stage

若只是要 debug 某個 build stage, 可以在 `docker build` 指定中止在哪個 stage
```shell
$ docker build --target STAGE_ALIAS_NAME . -t IMAGE:TAG
```

# Reference
* [Docker dock -- Use multi-stage builds](https://docs.docker.com/develop/develop-images/multistage-build/)
* [透過 Multiple Stage Builds 編譯出最小的 Docker Image](https://jiepeng.me/2018/06/09/use-docker-multiple-stage-builds)
* [How to remove intermediate images from a build after the build?](https://stackoverflow.com/a/55082473)
* [Docker docs -- Dockerfile reference -- LABEL](https://docs.docker.com/engine/reference/builder/#label)
* [Docker docs -- Dockerfile reference -- ARG](https://docs.docker.com/engine/reference/builder/#arg)
