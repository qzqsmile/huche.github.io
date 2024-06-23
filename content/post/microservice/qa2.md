---
title: "微服务的常见问题梳理-2"
date: 2022-02-23T11:42:45+08:00
lastmod: 2022-02-23T11:42:45+08:00
draft: true
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
  enable: true
  options: ""

sequenceDiagrams: 
  enable: true
  options: ""

---

### 服务注册与发现

#### 1. 什么是注册中心？

注册中心一般是微服务架构当中，用于解决服务发现问题的组件。我们可以将其认为成一个高可用的存储组件，主要用以存储一些服务地址信息。

#### 2. 服务注册与发现机制的基本模型是怎样的？

粗略版的流程如下:
1. 服务端开发完成，上线。
2. 将自己的服务注册到服务中心。
3. 客户端拉取到相应的服务的地址。

#### 3. 服务上线与服务下线的步骤是什么？

**服务上线:**
1. 服务端启动的时候，需要往注册中心里注册自身的信息，主要是定位信息。
2. 注册成功之后，注册中心和服务端要保持心跳（可省略）。
3. 客户端第一次发起对某个服务的调用之前，要先找注册中心获得所有可用服务节点列表，随后客户端会在本地缓存每个服务对应的可用节点列表。
4. 客户端和注册中心要保持心跳和数据同步，后续服务端有任何变动，注册中心都会通知客户端，客户端会更新本地的可用节点列表。
5. 客户端发送请求。
6. 服务端返回响应。

**服务下线:**
1. 服务端通知注册中心自己准备下线了。
2. 注册中心通知客户端某个服务端下线了。
3. 客户端收到通知之后，新来的请求就不会再给该服务端发过去。
4. 服务端等待一段时间之后，暂停服务并下线。

#### 4. 注册中心选型需要考虑哪些因素？

最重要的就是 AP 和 CP 的问题：

- 尽可能保证注册中心活着，但更大概率拿到错误的注册信息
- 还是尽可能拿到准确的注册信息，但注册中心可能崩溃

**注册中心的选择：**
- ZooKeeper/etcd -> CP
- Nacos/Eureka -> AP

#### 5. 为什么使用 Zookeeper/Nacos/etcd 作为注册中心？

- **Zookeeper**：适用于需要强一致性、分布式锁和主从选举的场景，广泛用于大数据和分布式系统中。
- **Nacos**：适用于微服务架构，特别是需要动态配置管理和健康检查的场景，集成了服务发现和配置管理。
- **etcd**：适用于需要高一致性和高可用性的场景，如 Kubernetes 和其他分布式系统，具有简单高效的特点。

#### 6. 什么是 CAP 定理？

CAP 定理（CAP theorem），又称布鲁尔定理（Brewer's theorem），是由计算机科学家 Eric Brewer 提出的一个定理。CAP 定理阐述了分布式计算系统中存在的一种权衡关系，指出在一个分布式数据存储系统中，不可能同时完美地实现以下三个特性：

- **一致性（Consistency）**：所有节点在同一时间看到相同的数据，即每次读操作都能读到最近写入的值。
- **可用性（Availability）**：每个请求（无论是读还是写）都会在合理的时间内得到响应，即系统始终是可用的，即使部分节点出现故障。
- **分区容错性（Partition Tolerance）**：系统在遭遇网络分区（即节点之间的通信中断）时仍然能够继续运作。

CAP 定理指出，一个分布式系统最多只能同时满足其中的两个特性，而无法同时满足全部三个特性。

#### 7. 在服务注册与发现里边是 AP 还是 CP？

实际上选择 C 还是 A，就是：

- 尽可能保证注册中心活着，但更大概率拿到错误的注册信息
- 还是尽可能拿到准确的注册信息，但注册中心可能崩溃

**服务注册中心的选择：**
- ZooKeeper/etcd -> CP
- Nacos/Eureka -> AP

#### 8. 如何保证服务注册与发现的高可用？

高可用的服务注册与发现要围绕注册服务端崩溃检测、客户端容错和注册中心选型三个方面进行。

#### 9. 服务器崩溃，如何检测？

此处主要讨论服务器崩溃的情况，不考虑相应网络的问题，网络的问题可以在客户端容错的层面解决。服务器崩溃，通常是通过服务注册中的 watch 机制来解决的。当其崩溃了，服务中心的地址信息也就不存在了，那么客户端同步也会删除该服务的信息。

#### 10. 客户端容错的措施有哪些？

两个方面：
1. 调用服务发现中心不通，这种情况使用本地的缓存就可以了。
2. 当调用服务端不通时，考虑将其在本地缓存当中标识为不可用。后续就不再对其进行调用了。同时客户端负责每隔一段时间检测其是否上线。

#### 11. 注册中心崩溃了怎么办？

首先注册中心一般很少崩，因为是多节点，其次万一崩了，客户端使用本地缓存的地址进行通信。但是这种情况下，新服务的上下线的功能就没有了。

#### 12. 注册中心怎么判断服务端已经崩溃了？

还是心跳，一般是通过内置的 Watch 方式来实现的，不会自己实现一套心跳包的机制。
