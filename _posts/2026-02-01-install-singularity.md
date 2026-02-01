---
layout: post
title: "Install Singularity on Ubuntu 24.04"
author: "Borting"
categories: journal
tags: [Singularity]
image: chimei-museum.jpg
---

# Install Dependencies

```shell
sudo apt-get update

sudo apt-get install -y \
    autoconf \
    automake \
    cryptsetup \
    fuse2fs \
    git \
    fuse3 \
    libfuse3-dev \
    libseccomp-dev \
    libtool \
    pkg-config \
    runc \
    squashfs-tools \
    squashfs-tools-ng \
    uidmap \
    wget \
    zlib1g-dev \
    libsubid-dev
```

# Download Debian Package

Download .deb package latest release from [Singularity release page](https://github.com/sylabs/singularity/releases), then install
```shell
sudo dpkg -i singularity-ce_4.3.7-noble_amd64.deb
```

# Start Container with Bash

Execute bash and load bashrc

```shell
SINGULARITY_SHELL=/bin/bash singularity run [IMAGE]
```

To overwrite shell prompt

```shell
SINGULARITY_SHELL=/bin/bash singularity run --env PS1="\[\e[94;1m\]\u@\h-singularity:\[\e[34m\w\e[0m\]\$ " [IMAGE]
```

Save bash history in another file to avoid origin bash history file from being removed

```shell
SINGULARITY_SHELL=/bin/bash singularity run \
	--env HISTFILE=/${HOME}/.sin_bash_history \
	--env PS1="\[\e[94;1m\]\u@\h-singularity:\[\e[34m\w\e[0m\]\$ " \
	[IMAGE]
```

# Resolve Conflict with Docker

If the server has installed docker-ce, install `runc` will autoly remove `containerd.io` and `docker-ce`.
This is because there is a runc binary in the containerd package.

We can check which version's runc is newer
```shell
runc --version
```

Generally, containerd.io does provide a latest version can cover the requirement of singularity.
Hence, install `containerd.io` and `docker-ce` back.
```shell
sudo apt install containerd.io docker-ce
```

# Reference

* [Singularity installation](https://github.com/sylabs/singularity/blob/main/INSTALL.md)
* [Singularity release page](https://github.com/sylabs/singularity/releases)
* [Why does Docker distribute containerd.io independently instead of using the upstream version?](https://github.com/containerd/containerd/discussions/11079#discussioncomment-11499823)
* [podman.io installation](https://podman.io/docs/installation#crun--runc)
* [The CentOS RPM package conflicts with the runc package from the OS](https://github.com/containerd/containerd/issues/5417#issue-866421783)
