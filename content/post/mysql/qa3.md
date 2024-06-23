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



#### 9. USE INDEX/FORCE INDEX/IGNORE INDEX有什么效果？

USE INDEX  
USE INDEX 提示优化器尽量使用指定的索引，但如果优化器认为使用其他索引更合适，仍然会选择其他索引。

FORCE INDEX  
FORCE INDEX 强制优化器使用指定的索引，即使优化器认为使用其他索引或不使用索引更好。

IGNORE INDEX  
IGNORE INDEX 告诉优化器在执行查询时忽略指定的索引。


#### 9. 使用索引有什么代价？


#### 10. 如何选择合适的索引列？组合索引里边怎么确定列的顺序？状态类的列是否适合作为索引的列？
高频的，这里的高频指的是比如select中出现较多，或者在order by，where等语句中出现较多的。
组合索引一般考虑最左前缀的原则，同时看看能不能避免一些回表的操作。
状态类一般不适合作为索引的列。

#### 11. 为什么MySQL使用B+树作为索引的数据结构？为什么不用B树？为什么不用红黑树？为什么不用二叉平衡树？为什么不用跳表？  



#### 12. NULL对索引有什么影响？

NULL 值在索引中占有条目，并且在查询、范围查询和排序时会影响索引的使用和性能。
在组合索引中，如果最左列包含 NULL 值，索引可能仍然有效。但非最左列包含 NULL 值时，查询优化器可能会选择不使用该索引。


#### 13. 唯一索引是否允许多个NULL值？

在 MySQL 的 InnoDB 存储引擎中，唯一索引允许多个 NULL 值，因为 NULL 被视为未知的，不会被视为重复值。

