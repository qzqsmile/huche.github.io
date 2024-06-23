---
title: "MySQl的问题梳理-1"
date: 2022-03-23T17:05:07+08:00
lastmod: 2022-03-23T17:05:07+08:00
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

### 数据库索引

#### 1. 什么是覆盖索引？

覆盖索引（Covering Index）是数据库查询优化中的一种技术，指的是查询所需的所有字段都可以通过索引获取，而不需要访问数据表中的实际行。这种技术能够显著提高查询性能，因为数据库系统只需要访问索引而不需要回表（即访问数据表中的实际数据）。

#### 2. 什么是聚簇索引/非聚簇索引？

在 MySQL 中，聚簇索引（也称为簇集索引）是表中数据的物理存储顺序与索引顺序一致的索引。InnoDB 存储引擎默认情况下使用聚簇索引，主键索引是默认的聚簇索引。  
在 MySQL 中，非聚簇索引（也称为二级索引或辅助索引）是指索引的顺序与数据的物理存储顺序无关的索引。非聚簇索引的叶节点存储索引键和指向数据行的指针（主键值）。

#### 3. 什么是哈希索引？MySQL InnoDB引擎怎么创建一个哈希索引？

哈希索引（Hash Index）是一种基于哈希表的数据结构的索引。哈希索引通过对索引键应用哈希函数，将键映射到哈希表中的一个位置，从而实现快速查找。哈希索引通常用于需要高效等值查询的场景，例如查找特定键值。  
在 MySQL InnoDB 存储引擎中，默认情况下不支持显式创建哈希索引。InnoDB 使用 B+ 树结构来实现聚簇索引和非聚簇索引。然而，InnoDB 支持一种称为自适应哈希索引（Adaptive Hash Index, AHI）的机制，这是 InnoDB 的一种内部优化功能，可以在某些情况下自动创建哈希索引以提高查询性能。


#### 4. 什么回表？如何避免回表？


#### 5. 树的高度和查询性能是什么关系？



#### 6. 什么是索引最左匹配原则？


#### 7. 范围查询，Like之类的查询怎么影响数据库使用索引？


#### 8. 索引是不是越多越好？


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

