---
layout: post
title: "Inspect Labels of Singularity Images"
author: "Borting"
categories: journal
tags: [Singularity]
image: chimei-museum.jpg
---

紀錄如何看 Singularity image labels.

# Definition File

```text
%labels
    Author borting@my.workshops.com
    Version v1.0.0
```

# Inspection Command

```shell
singularity inspect --labels IMAGE.SIF
```

# Reference

* [The inspect command](https://docs.sylabs.io/guides/3.2/user-guide/environment_and_metadata.html#the-inspect-command)
