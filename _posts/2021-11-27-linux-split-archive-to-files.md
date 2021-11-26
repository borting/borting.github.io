---
layout: post
title: "Split Large Archive into Multiple Files"
author: "Borting"
categories: journal
tags: [Linux]
image: Jokulsarlon.jpg
---

紀錄一下在 Linux 上要如何把壓縮檔(tar or zip)拆成數個小檔案.

# Tar Archive

適用於所有與 tar 搭配的壓縮方式 (gzip, bzip2, xz, etc.)

## Compress and Split

以 gzip 為例

* Compress a folder and split
```shell
# Each splitted file has maximum 10MB in size
$ tar czf - myfolder | split -b 10M - "myarchive.tar.gz.part"
```

The results splitted files would be named in the following series, "myarchive.tar.gz.partaa", "myarchive.tar.gz.partab", ...

* Split a compressed archive
```shell
$ cat original.tar.gz | split -b 10M - "myarchive.tar.gz.part"
```
```shell
$ split -b 10M original.tar.gz "myarchive.tar.gz.part"
```

## Merge and Decompress

* Merge splitted files
```shell
$ cat myarchive.tar.gz.part* > merged.tar.gz
```

* Decompress splitted archives directly
```shell
$ cat myarchive.tar.gz.part* | tar xzf
```

# Zip Archive

## Compress and Split
```shell
# Each splitted archive has 5 MB in size
$ zip -r -s 5m myarchive.zip myfolder/
```
The results would be "myarchive.zip", "myarchive.z01", "myarchive.z02", "myarchive.z03", ...

## Merge and Decompress
```shell
$ zip -F myarchive.zip --out single-archive.zip
$ unzip single-archive.zip
```

# Reference
* [How to split tar archive into multiple blocks of a specific size](https://linuxconfig.org/how-to-split-tar-archive-into-multiple-blocks-of-a-specific-size)
* [How to Split Large ‘tar’ Archive into Multiple Files of Certain Size](https://www.tecmint.com/split-large-tar-into-multiple-files-of-certain-size/)
* [How to split zip archive into multiple blocks of a specific size](https://linuxconfig.org/how-to-split-zip-archive-into-multiple-blocks-of-a-specific-size)
