---
layout: post
title: "Configure Locale for Docker Images"
author: "Borting"
categories: journal
tags: [Docker]
image: chimei-museum.jpg
---

紀錄一下如何用 Dockerfile 設定 Docker images 的 locale.

# Example

以設成 `en_US.UTF-8` 為範例的 Dockerfile 如下:
```
FROM ubuntu:20.04

RUN \
	# Update APT info
	apt update; \
	
	# Install required package
	apt install -y locales; \
	
	# Set the locale
	sed -i '/en_US.UTF-8/s/^# //g' /etc/locale.gen && locale-gen; \
	
	# Last line of installation and clean apt cache
	rm -rf /var/lib/apt/lists/* && apt clean && apt autoclean; \
	
	# Last Line of Docker RUN command
	:;

# Configure environemnt variables
ENV LANG="en_US.UTF-8" LANGUAGE="en_US:en" LC_ALL="en_US.UTF-8"

WORKDIR "/root/"
```

# Reference
* [Docker and Locales](http://jaredmarkell.com/docker-and-locales/)
* [How to set the locale inside a Debian/Ubuntu Docker container?](https://stackoverflow.com/a/28406007)
