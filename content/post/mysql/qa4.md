---
title: "MySQl的问题梳理-4"
date: 2022-04-07T18:05:07+08:00
lastmod: 2022-04-07T18:05:07+08:00
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

### MVCC协议

#### 1.什么是行锁，表锁？为什么需要MVCC?
行锁: 即给某一行加锁你  
表锁: 即给表加锁 
使用场景:  


#### 2. 什么是乐观锁？怎么在MySQL里边实现一个乐观锁？
基本的步骤都是先拿到某个变量的值。  
中间进行一通操作。  
最后如果变量的值没有变化的话则更新。  
这里需要注意乐观锁，加锁的目标一定是个并发场景下一定会修改的变量，否则会有问题。  

#### 3. 什么是意向锁？可以举一个例子吗？  
意向锁，主要是为了协调表锁和行锁之间的关系，设想如下场景。主要避免的情形为:在某个事务想加表锁的情况下，无需扫描全表，判断其是否有行锁，导致其无法添加。  
意向锁，可以理解为一个表示该表有行锁的表锁。

#### 4. 什么是共享锁和排它锁，它们有什么特性？
共享锁用于读取数据，允许多个事务同时读取相同数据，但不允许其他事务进行写操作，从而保证了读取的一致性。  
排它锁用于修改数据，只允许一个事务对数据进行写操作，同时阻止其他事务读取或写入相同数据，确保了数据的独占性和完整性。  
共享锁和排它锁之间是互斥的，一个事务持有排它锁时，其他事务不能同时持有共享锁或排它锁，反之亦然。


#### 5. 什么是两阶段锁？
当一个事务获取到了某一个数据库对象的锁之后，并不是当前事务不需要操作它了之后，这个说就会马上释放掉，这个锁会一直被这个事务持有，直到这个事务被提交或回滚后，这个锁才会被释放掉。
本质上是说加锁的时机和释放锁的时机。

#### 6. 什么是记录锁,间隙锁和临键锁？
记录锁指的是锁住了某一个记录的锁。-行锁的一种  
间隙锁指的是锁住了某一段范围的锁。-行锁的一种 
临键锁 左开右边的一种锁。



#### 7. RC级别有间隙锁和临键锁吗？



#### 8. MySQL是怎么在PR级别下解决幻读的？
主要通过记录锁,间隙锁和临键锁来实现的


#### 9. 什么情况下会加临键锁？什么情况下会加间隙锁？什么时候加记录锁？



#### 10.唯一索引和普通索引会怎么影响锁？
普通索引在查询和插入时通常不会引入额外的临键锁或间隙锁，除非显式要求使用行级锁。  
唯一索引在插入数据时会引入临键锁和间隙锁，以确保插入的数据不会违反唯一性约束，从而保证数据的完整性。  


#### 11. 你遇到过什么死锁问题吗？怎么排查的？最终又是怎么解决的？ 



#### 12. 你有没有优化过锁？怎么优化的？


