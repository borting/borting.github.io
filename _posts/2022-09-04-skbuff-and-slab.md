---
layout: post
title: "Skbuff and Slab Allocator"
author: "Borting"
categories: journal
tags: [Linux]
image: Jokulsarlon.jpg
---

Slab alloction
* retains allocated memory containing a data object of a certain type for reuse upon subsequent allocations of objects of the same type
* kernel allocates a number of pre-allocated "slabs" of memory for a cetain type of obecjt, and within each slab there are memory chunks of fixed size suitable for that objects
* eliminates the need to search for suitable memory space for allocating objects
* reduces fragmentation caused by allocations and deallocations

Teminology
* Cache: a storage for a specific type of object. A cache may contain several slabs.
* Slab: a contiguous piece of physical memory. A slab contains server objects.


kmalloc() 會透過 slab allocator, 但 vmalloc() 不會.
- Base: page allocator (zoned buddy allocator)
- Built on top of page allocator: slab allocator, vmalloc(), user-space memory allocator


Buddy allocator 
* maintain directly-mapped table for memory blocks of various orders
* The bottom level table contains the map for the smallest allocatable units of memory (i.e. pages)
* Each level above it describes pairs of units from the levels below – buddies.
* check '/proc/buddyinfo'



sk\_buff 會使用 slab allocation 來減少頻繁的跟 kernel 要新的 page, 也避免出現 external fragmentation.
(參考 [kernel source code](https://elixir.bootlin.com/linux/v5.16.10/source/net/core/skbuff.c#L4474)).)

可以從以下地方看目前 sk\_buff 有多少個 slab
```shell
$ cat /proc/slabinfo | grep skbuff
```
會看到
```
name            <active_objs> <num_objs> <objsize> <objperslab> <pagesperslab> : tunables <limit> <batchcount> <sharedfactor> : slabdata <active_slabs> <num_slabs> <sharedavail>

...

skbuff_fclone_cache      8     10    736    5    1 : tunables   54   27    8 : slabdata      2      2      0
skbuff_head_cache  15637  15670    384   10    1 : tunables   54   27    8 : slabdata   1567   1567     27
```

若找不到, 則有可能是因為 kernel 打開了 `CONFIG_SLUB`, 相同大小的 slab cache 被合併了.
可以從以下地方看 skbuff\_head\_cache 是與哪個 slab cache 合併
```shell
$ ll /sys/kernel/slab/ | grep skbuff
```
得到
```
lrwxrwxrwx   1 root root 0 Feb 23 15:04 skbuff_fclone_cache -> :0000512/
lrwxrwxrwx   1 root root 0 Feb 23 15:04 skbuff_head_cache -> :0000256/
```

# Reference
* [How SKBs work](http://vger.kernel.org/~davem/skb_data.html)
* [skbuff_head_cache去哪里了](https://blog.csdn.net/phenix_lord/article/details/48165511)
* [skb_init(void)](https://elixir.bootlin.com/linux/v5.16.10/source/net/core/skbuff.c#L4474)
* [kvmalloc()](https://lwn.net/Articles/711653/)
* [Memory management Buddy allocator Slab allocator](https://students.mimuw.edu.pl/ZSO/Wyklady/06_memory2/BuddySlabAllocator.pdf)
