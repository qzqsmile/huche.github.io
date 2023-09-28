---
title: "Mysql45讲笔记(上)"
date: 2020-09-20T21:21:19+08:00
lastmod: 2023-09-20T21:21:19+08:00
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


这里主要列出的是极客时间Mysql45讲的思维脑图的笔记。
Mysql是如何执行的。

1. Mysql的日志系统

{{< figure src="/images/storage/mysql_1.png" alt="Image Alt Text" title="" >}}

1. 事务隔离，为何你的事务看不见

{{< figure src="/images/storage/mysql_2.png" alt="Image Alt Text" title="" >}}

1. 索引相关知识

{{< figure src="/images/storage/mysql_3.png" alt="Image Alt Text" title="" >}}

1. 数据库里的锁 
{{< figure src="/images/storage/mysql_4.png" alt="Image Alt Text" title="" >}}

1. 数据库里的行锁
{{< figure src="/images/storage/mysql_5.png" alt="Image Alt Text" title="" >}}

重复读模式下事务的实现
{{< figure src="/images/storage/mysql_6.png" alt="Image Alt Text" title="" >}}

普通索引与唯一索引的异同
{{< figure src="/images/storage/mysql_5.png" alt="Image Alt Text" title="" >}}


为什么选错索引
{{< figure src="/images/storage/mysql_5.png" alt="Image Alt Text" title="" >}}


创建字符串索引的一些方法
{{< figure src="/images/storage/mysql_5.png" alt="Image Alt Text" title="" >}}


为何数据库抖了一下
{{< figure src="/images/storage/mysql_5.png" alt="Image Alt Text" title="" >}}


删了数据为何空间没减少
{{< figure src="/images/storage/mysql_5.png" alt="Image Alt Text" title="" >}}


count为啥那么慢
{{< figure src="/images/storage/mysql_5.png" alt="Image Alt Text" title="" >}}


问题解析
{{< figure src="/images/storage/mysql_5.png" alt="Image Alt Text" title="" >}}


orderby是如何执行的
{{< figure src="/images/storage/mysql_5.png" alt="Image Alt Text" title="" >}}


随机获取一个值
{{< figure src="/images/storage/mysql_5.png" alt="Image Alt Text" title="" >}}


为何没用索引
{{< figure src="/images/storage/mysql_5.png" alt="Image Alt Text" title="" >}}


SQL语句为何执行那么慢
{{< figure src="/images/storage/mysql_5.png" alt="Image Alt Text" title="" >}}


幻读
{{< figure src="/images/storage/mysql_5.png" alt="Image Alt Text" title="" >}}


ToDo
{{< figure src="/images/storage/mysql_5.png" alt="Image Alt Text" title="" >}}


有哪些饮鸩止渴的方法
{{< figure src="/images/storage/mysql_5.png" alt="Image Alt Text" title="" >}}


binlog&redo log 怎么保证数据完整
{{< figure src="/images/storage/mysql_5.png" alt="Image Alt Text" title="" >}}


主备如何保持一致
{{< figure src="/images/storage/mysql_5.png" alt="Image Alt Text" title="" >}}


如何保证高可用性
{{< figure src="/images/storage/mysql_5.png" alt="Image Alt Text" title="" >}}


切换点怎么缺点
{{< figure src="/images/storage/mysql_5.png" alt="Image Alt Text" title="" >}}



如何判断mysql是否可用
{{< figure src="/images/storage/mysql_5.png" alt="Image Alt Text" title="" >}}


<!--more-->
