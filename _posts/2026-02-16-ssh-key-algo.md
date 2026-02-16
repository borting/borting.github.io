---
layout: post
title: "SSH Key: RSA vs Ed25519"
author: "Borting"
categories: journal
tags: [Linux]
image: Jokulsarlon.jpg
---

# Comparison

* Ed25519: Shorter key, but may not be supported on old SSH server
* RSA: widely used, but require at least 4096 bits in key length

# Generate Ed25519 Keys

```shell
ssh-keygen -t ed25519 -f ~/.ssh/your-key-filename -C "your-key-comment"
```

# Reference

* [SSH Key Best Practices for 2025 – Using ed25519, key rotation, and other best practices](https://www.brandonchecketts.com/archives/ssh-ed25519-key-best-practices-for-2025)'
* [How do Ed5519 keys work?](https://blog.mozilla.org/warner/2011/11/29/ed25519-keys/)
* [選擇 SSH key 的加密演算法](https://medium.com/@honglong/%E9%81%B8%E6%93%87-ssh-key-%E7%9A%84%E5%8A%A0%E5%AF%86%E6%BC%94%E7%AE%97%E6%B3%95-70ca45c94d8e)
