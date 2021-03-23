---
layout: post
title: "Network Device Counter Wrapping Around"
author: "Borting"
categories: journal
tags: [Linux,driver]
image: Jokulsarlon.jpg
---

前陣子遇到了一個很妙的 bug: 在 32-bit CPU 的 Linux 平台上透過 wifi 下載一個超過 4GB 的檔案後, ifconfig 看到的 Rx bytes coutner 發生了 wrapping around (溢位).
但神奇的是, 相同的 wifi driver 在另一個 64-bit CPU 的 Linux 平台上跑同樣的測試, 卻不會有溢位問題.

# Network Device Counter Structure

既然發生溢位, 就先從 Linux network device counter 的資料結構開始看吧.

## net_device_stats

出問題的 driver 使用的 device counter 結構是 [`struct net_device_stats`](https://elixir.bootlin.com/linux/v5.11/source/include/linux/netdevice.h#L169), 結構裡的變數都是宣告成 `unsigned long`.
`unsigned long` 在 32-bit Linux 平台的長度是 32 bits, 所以超過 2 ^ 32 就會發生溢位.

```c
// include/linux/netdevice.h
struct net_device_stats {
	unsigned long	rx_packets;
	unsigned long	tx_packets;
	unsigned long	rx_bytes;
	unsigned long	tx_bytes;
	...
};
```

## rtnl_link_stats64

Linux 很早就注意到 `struct net_device_stats` 在 32-bit 環境中潛在的溢位問題, 所以在 3.0 提出了 64-bit 版本的資料結構 – [`struct rtnl_link_stats64`](https://elixir.bootlin.com/linux/v5.11/source/include/uapi/linux/if_link.h#L215).
結構裡的變數全部宣告成 `__u64`, 所以最多可以紀錄到 2 ^ 64.

```c
// include/uapi/linux/if_link.h
struct rtnl_link_stats64 {
	__u64	rx_packets;
	__u64	tx_packets;
	__u64	rx_bytes;
	__u64	tx_bytes;
	...
};
```

## Usage

Linux 的 network driver 需要實做 hook function 讓 kernel 得以存取 device counter.
存取 `struct net_device_stats` 要實做 [`ndo_get_stats()`](https://elixir.bootlin.com/linux/v5.11/source/include/linux/netdevice.h#L1326), 存取 `struct rtnl_link_stats64` 則要實做 [`ndo_get_stats64()`](https://elixir.bootlin.com/linux/v5.11/source/include/linux/netdevice.h#L1320).

```c
// include/linux/netdevice.h
struct net_device_ops {
	...
	struct net_device_stats* (*ndo_get_stats)(struct net_device *dev);
	...
	void (*ndo_get_stats64)(struct net_device *dev,
		struct rtnl_link_stats64 *storage);
	...
};
```

kernel 則提供 [`dev_get_stats()`](https://elixir.bootlin.com/linux/v5.11/source/net/core/dev.c#L10404) API 讓其他 kernel component 存取 device counter.
若 `ndo_get_stats64()` hook 有被實做, 則 `dev_get_stats()` 會優先呼叫 `ndo_get_stats64()` 存取 `struct rtnl_link_stats64` device counter.
若無, 則呼叫 `ndo_get_stats()`, 並將存放於 `struct net_device_stats` 的結果轉換成 `struct rtnl_link_stats64` 結構回傳.

```c
// https://elixir.bootlin.com/linux/v5.11/source/net/core/dev.c
struct rtnl_link_stats64 *dev_get_stats(struct net_device *dev,
	struct rtnl_link_stats64 *storage)
{
	const struct net_device_ops *ops = dev->netdev_ops;

	if (ops->ndo_get_stats64) {
		memset(storage, 0, sizeof(*storage));
		ops->ndo_get_stats64(dev, storage);
	} else if (ops->ndo_get_stats) {
		netdev_stats_to_stats64(storage, ops->ndo_get_stats(dev));
	} else {
		netdev_stats_to_stats64(storage, &dev->stats);
	}
	...
	return storage;
}
```

# Size of Unsigned Long

出問題的 wifi driver 只實做了 `ndo_get_stats()` hook, 所以上層透過 `dev_get_stats()` 拿到的也只是 32-bit 轉換成 64-bit 的資料.
那在 64-bit CPU 的 Linux 平台上怎不會發生溢位?

其實, 魔鬼的細節藏在 struct net_device_stats 裡變數的型態 – `unsigned long`.
`unsigned long` 在 32-bit Linux 上的長度 32 bits, 但在 64-bit Linux 上的長度是 64-bits.
所以, 在 64-bit Linux 上, 就算 wifi drier 只實做了 `ndo_get_stats()` hook, 回傳的 `struct net_device_stats` 裡的變數的長度都已經變成 64 bits 了, 不會有溢位的情況發生.

```c
//include/linux/netdevice.h
struct net_device_stats {
	unsigned long	rx_packets;
	unsigned long	tx_packets;
	unsigned long	rx_bytes;
	unsigned long	tx_bytes;
	...
};
```

這裡要小小澄清一下: 我前面的描述混用了 64-bit CPU 和 64-bit Linux 這兩個詞, 但兩者其實不應混用.
問題裡的 64-bit CPU 平台上執行的是 64-bit Linux, 所以 `struct net_device_stats` 裡的變數的長度都是 64 bits.
如果 64-bit CPU 平台執行的是 32-bit Linux 會發生什麼事呢?
(嘿嘿嘿~)
另外, 為什麼 64-bit Linux 上的 `unsigned long` 長度是 64 bits 呢?
這其實和作業系統的 [`data model`](http://nickdesaulniers.github.io/blog/2016/05/30/data-models-and-word-size/) 有關.
這些就留到下一篇再來解釋吧.

# Conslusion

不管是在 32-bit 還是 64-bit Linux 平台上, 透過 `dev_get_stats()` 拿到的都是 64-bit 的資料結構 `struct rtnl_link_stats64`.
但 device counter 的上限取決於: (1) unsigned long 的長度 (32 or 64 bits), (2) driver 實做了哪個 hook (`ndo_get_stats()` or `ndo_get_stats64()`).
要確保在兩平台都不會發生溢位, 則 driver 應該要實做 `ndo_get_stats64()` hook, 提供 64-bit 的 counter 資料.
