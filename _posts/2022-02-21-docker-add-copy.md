---
layout: post
title: "Difference between Docker ADD and COPY commands"
author: "Borting"
categories: journal
tags: [container, Docker]
image: chimei-museum.jpg
---

這篇紀錄一下 Dockerfile 中 `ADD` 和 `COPY` 使用上的差異.
先講結論: 除了新增一個需要解壓縮的檔案到 image 時使用 `ADD` 外, 絕大部分的情況下都建議使用 `COPY`.

# ADD

* Add multiple files to image (The `<src>` path must be inside the context of the build)
```Dockerfile
ADD [--chown=<user>:<group>] <src>... <dest>
ADD [--chown=<user>:<group>] ["<src>",... "<dest>"]
```

* Add local tar file to image and extraction
```Dockerfile
ADD archive.tar.gz /
```

# COPY 

* Copy multiple files to image (The `<src>` path must be inside the context of the build)
```Dockerfile
COPY [--chown=<user>:<group>] <src>... <dest>
COPY [--chown=<user>:<group>] ["<src>",... "<dest>"]
```

* Copy from previous build stage results
```Dockerfile
COPY --from= ...
```

# Common Characteristics

* `COPY` 和 `ADD` 都只能存取 context 中的檔案/資料夾, 並把他們放到 image 中

* 如果 `<src>` 是資料夾, 則 `<dest>` 是一個 `/` 結尾的資料夾, 則 `<src>/` 資料夾下所有的內容會被搬到 `<dest>/` 下
```Dockerfile
# Copy contents of go directory to /usr/local/
COPY go /usr/local/

# This equals to
COPY go/* /usr/local/
```

* 如果 `<src>` 是資料夾, 則 `<dest>` 是一個非 `/` 結尾且不存在的位址, 則 Docker 會新建一個 `<dest>` 資料夾, 並把 `<src>` 資料夾下所有的內容搬過去
```Dockerfile
# Copy go directory to /usr/local
COPY go /usr/local/go
```

* 兩個指令都支援指定多個 `<src>` 位址, 此時 `<dest>` 必須是一個包含 slash `/` 的資料夾位址.
`<src>` 中所有的 content (不包含 `<src>`) 都會被加到 `<dest>` 中.
```Dockerfile
COPY dir1 dir2 ./
# equals to
COPY dir1/* dir2/* ./
```

# Reference

* [Docker Documentation -- Dockerfile best practices -- ADD or COPY](https://docs.docker.com/develop/develop-images/dockerfile_best-practices/#add-or-copy)
* [How to copy multiple files in one layer using a Dockerfile?](https://stackoverflow.com/a/30316600)
* [Copy directory to another directory using ADD command](https://stackoverflow.com/a/26504961)
