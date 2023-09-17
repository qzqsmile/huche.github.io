---
title: "网络相关的知识"
date: 2020-08-04T23:19:30+08:00
lastmod: 2023-09-19T23:19:30+08:00
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

<!--more-->

计算机网络的一些知识点总结
## TCP/IP相关
TCP/IP的建立前期是三次握手，后期是四次挥手。其这种机制主要解决了超时和重传的问题。这两个问题在计算机网路中是普遍存在的问题。 其中TCP/IP有一些小的细节还是需要我们注意的

## Delay ACK
为了减少网络中小的数据片段，TCP默认是采用DelayAck的模式，默认是40ms。即ACK的发送回延迟40ms，把40ms中的所有数据包汇集到一起进行发送。这里需要注意，Delay Ack虽然减少了网络数据包，但是在一些延迟比较敏感的地方（比如手游），这个默认是要关闭的。

## Nagle
这个也是一个用于减少网络中数据的算法，与Delay ACK的思想不同，它是通过数据包的控制来实现减少数据段的。简而言之，对数据量小于MSS的数据包不进行发送，而是等所有的数据到达MSS的大小再进行发送。

## 流量&拥塞管理
流量管理的主要是利用经典的窗口协议进行实现的。 拥塞管理算法则是利用慢启动&快重传等一些算法进行实现的。 其中慢启动的思想是逐步（一般是2倍的关系）加大传递数据的速率，知道发生拥塞，其中发生拥塞主要是通过检测 超时来实现的。如果发现连续收到多个ACK，并不能说明其发生了拥塞，而是应该推断其某个数据包丢失，这里就会触发快重传的机制。然后发送端也会执行快恢复的算法，将发送数据的速率降低。

## TIME_WAIT
TIME_WAIT的存在主要是两个功能，1.防止最后的一个ACK没有送达，进行重传。 2。防止其关闭后，但是对方又没关闭。则有可能同时与两个客户端产生连接。比如之前是 A<->B 后边可能会编程A<->C B->A。即B依旧认为这个连接是活跃的。

## select&poll&epoll
这里主要是select vs epoll的区别，poll的原理与select类似。 select用于网络监听的效率比较低主要是由于两个方面

1. 你进行select监听端口时，如果有数据发来时，需要进行两次复杂度为O(n)的遍历。第一次进行监听时，要将所有的监听端口都传到内核，第二次是当监听到有数据在锁监听的端口时，则需要遍历所有监听的文件描述符。看看究竟是哪个文件描述符需要读取数据
2. 由1可以看到其中需要在内核态和用户态大量的传输数据，这样非常消耗性能。
epoll 则主要对select进行了优化 构造了一个eventpool的结构，用于存放监听的接口，与select不同的是 epoll平常之事对这个结构体进行CRUD之类的维护。所以不会每次都传送所有的文件描述符的情况。另外 构造了一个数据结构rdlist用于存放可读写的sock，所以也不用遍历一遍所有的文件描述符看看哪些可读了。 子宫eventpool 利用红黑树进行实现，为了更好的查找是否是重复的socket id ffffffffffffffffffffffvcxXS rdlist则是双向链表。

另外epoll 支持ET模式，即边沿触发，只触发一次时间。而LT则是如果数据没读取完则一直触发。一般而言ET的效率稍高，使用逻辑更复杂。所以一般使用LT的情形比较多，其性能已可以满足大多数情形