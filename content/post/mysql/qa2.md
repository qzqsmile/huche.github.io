---
title: "MySQl的问题梳理-2"
date: 2022-04-01T17:05:07+08:00
lastmod: 2022-04-02T17:05:07+08:00
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

### SQL优化

#### 1.EXPlAIN命令

通过使用 EXPLAIN 命令，可以深入了解 MySQL 查询的执行计划，从而发现和解决性能瓶颈。利用 EXPLAIN 提供的信息，可以针对特定查询优化索引、重写查询语句和调整表结构，以提高查询性能。

#### 2. SQL具体是怎么优化的？  


#### 3. 索引是怎么优化的？


#### 4. 怎么优化COUNT查询？


#### 5. 怎么优化ORDER BY？


#### 6. 怎么优化LIMIT OFFSET查询  
LIMIT OFFSET慢的原因在于其总是读取所有的数据，然后舍弃limit后边的数据。这样当limit后接的数据非常大的时候，读取的数据过多，会导致速度过慢。

#### 7. 为什么要尽量吧条件写到WHERE而不是写到HAVING里边？


#### 8. 怎么给一张表添加新的索引/修改表结构？如果我的数据量很大呢？

1.停机变更
2.数据流量低谷时变更
3. 新建表变更

#### 9. USE INDEX/FORCE INDEX/IGNORE INDEX有什么效果？

USE INDEX  
USE INDEX 提示优化器尽量使用指定的索引，但如果优化器认为使用其他索引更合适，仍然会选择其他索引。

FORCE INDEX  
FORCE INDEX 强制优化器使用指定的索引，即使优化器认为使用其他索引或不使用索引更好。

IGNORE INDEX  
IGNORE INDEX 告诉优化器在执行查询时忽略指定的索引。

