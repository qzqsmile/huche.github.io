---
title: "Go的一些知识点"
date: 2020-10-03T22:16:39+08:00
lastmod: 2023-09-27T22:16:39+08:00
draft: false
keywords: []
description: ""
tags: []
categories: []
author: ""

# You can also close(false) or open(true) something for this content.
# P.S. comment can only be closed
comment: false
toc: false
autoCollapseToc: false
postMetaInFooter: false
hiddenFromHomePage: false
# You can also define another contentCopyright. e.g. contentCopyright: "This is another copyright."
contentCopyright: false
reward: false
mathjax: false
mathjaxEnableSingleDollar: false
mathjaxEnableAutoNumber: false

# You unlisted posts you might want not want the header or footer to show
hideHeaderAndFooter: false

# You can enable or disable out-of-date content warning for individual post.
# Comment this out to use the global config.
#enableOutdatedInfoWarning: false

flowchartDiagrams:
  enable: false
  options: ""

sequenceDiagrams: 
  enable: false
  options: ""

---

1. 内存分配
微小对象，size < 16B
一般小对象， 16 bytes < size <= 32k
大对象 size > 32k 对应的分配方式分三种：
tinny allocations (size < 16 bytes，no pointers) 微型分配器分配。
small allocations ( size <= 32k) 正常分配；首先通过计算使用的大小规格，然后使用 mcache 中对应大小规格的块分配
large allocations (size > 32k) 大对象分配；直接通过mheap分配。这些大对象的申请是以一个全局锁为代价的，因此任何给定的时间点只能同时供一个 P 申请。 其中tiny allocation主要是利用mcache进行分配，mcache里默认有个一块大小为16B的内存供其进行分配使用，当这块内存完全占用后。会走small allocation 对于small allocations是golang分配最复杂的一部分，这里仅简单梳理下分配内存的流程，对于申请size 为n的内存其分配步骤如下。
获取当前的mcache
根据size算出合适的class ID
从mache的alloc[class]中找出可用的span
如果mache没有可用的span，从mcentral中申请一个span加入
如果mcentral也没有可用的，则从mheap中申请一个加入
从span中返回对象地址。 large allocations相对简单的多，直接从mheap上分配。

2. 写屏障 golang的垃圾回收是基于标记清除的技术实现的，该技术都会有一个明显的缺陷就是STW。即Stop the world.即程序暂停，进行内存回收，写屏障即是消除此种影响的一种技术。 写屏障主要分三种 插入写屏障 删除写屏障 混合写屏障
其中go主要实现的的是插入写屏障，1.8后实现的是混合写屏障。 写屏障主要思想是为了防止STW，对于某些本可以进行内存回收对象，这次GC选择性的忽略，等待下次再进行内存回收。

这里就牵扯到强三色一致性协议和弱三色一致性协议了。 只要满足强三色一致性协议或者弱三色一致性协议，就能保证GC回收是正确的（但不一定是最优的）。

强三色不变性 — 黑色对象不会指向白色对象，只会指向灰色对象或者黑色对象； 弱三色不变性 — 黑色对象指向的白色对象必须包含一条从灰色对象经由多个白色对象的可达路径

插入写屏障： go最原始的一种算法，即黑色对象指向黑色时，会强行置灰。伪代码如下

```golang 
writePointer(slot, ptr)
  shade(*slot)
  *slot = ptr
```

好处 1.实现简单 缺点1.因为不能对stack上运用写屏障，否则太影响性能，所以在最后还是需要STW来进行栈上的回收

删除写屏障 go并木有实现过这种方法，满足的是弱三色请求。

```golang 
writePointer(slot, ptr)
  shade(*slot)
  *slot = ptr
```

优点: 1.结束不用STW 缺点: 1. 开始时要启动STW，扫描全栈进行保护，因为栈上默认不进行标记和清除的操作 2. 最后可能会写元素应该回?收到没有回收 混合写屏障: 其实是删除屏障+写屏障的结合。这里考虑删除写屏障刚开始的需要STW的情形，为了减少这部分STW的时间。混合写屏障改为并发的进行栈扫描，但是为了防止一个不该回收的对象回收了，这里加上了写屏障的操作，这样就使的其可以混合扫描。 具体例子，可见此文 所以其优缺点如下: 优: 1. 不用STW，即使刚开始的扫栈也是并发执行的。 缺点： 1. 回收精度与删除写屏障相似，有些本可以回收的内存可能仍回收不了。

3. nil to do

unsafe.pointer 为了实现golang进行指针转换的library. 因为unsafe.pointer的底层其实也就是个*int类型。但是如果你声明一个type myPointer *int 类型的struct，其实是起不到unsafe.pointer同样的作用的。所以我怀疑这里应该是编译器层面对unsafe.pointer做了特殊的处理，使的其实现了指针的转换功能。

4. golang抢占式调度 to do

5. atomic.Value to do

6. go runtime



优点: 1.结束不用STW 缺点: 1. 开始时要启动STW，扫描全栈进行保护，因为栈上默认不进行标记和清除的操作 2. 最后可能会写元素应该回?收到没有回收 混合写屏障: 其实是删除屏障+写屏障的结合。这里考虑删除写屏障刚开始的需要STW的情形，为了减少这部分STW的时间。混合写屏障改为并发的进行栈扫描，但是为了防止一个不该回收的对象回收了，这里加上了写屏障的操作，这样就使的其可以混合扫描。 具体例子，可见此文 所以其优缺点如下: 优: 1. 不用STW，即使刚开始的扫栈也是并发执行的。 缺点： 1. 回收精度与删除写屏障相似，有些本可以回收的内存可能仍回收不了