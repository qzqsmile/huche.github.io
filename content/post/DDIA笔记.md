---
title: "DDIA笔记"
date: 2021-02-03T23:30:46+08:00
lastmod: 2023-09-27T23:30:46+08:00
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


ch1 Three key criteria for data system. That’s Reliable, Scalable, and Maintainable.

Maintainalbe means the data system is very easy to main by others.

ch2

这一章节主要总结数据模型的发展的问题。 要想理解本章，最好先思考下如果是让我来设计，应该如何存储数据。 就拿本章的linkedin来举例子，最为直接的想法就是将一个人的resume存成一个record。 这种方法其实就是最原始的document module的思想，但是这种方法会有一些很难克服的弊端

*   随着用户增长，数据量会急剧增长，因为冗余数据会非常多。比如所有的美国用户都会存美国这个字段
*   Update一些公共的字段会很复杂，比如有个university的description需要更改，需要改所有该学校的学生的resume
*   对并发支持会很差，应该数据大量聚集在一个table，所有的操作，比如search都会遍历该table。会导致热点集中

当然有缺点必定也有优点。

*   对Scheme要求不严格
*   更加简单，某些情形下可以简化代码

为了克服以上的缺点，需要数据模型支持Join的操作，历史上有两个发展思路。

*   network Model: 改模型虽然很符合人的逻辑，把相应的relattion都存下来，但是因为没有相应的约束(比如Relational Model里的Scheme)，当数据量大时，会导致这种relation存的异常复查，导致程序编写的困难。
*   Relational model: 这种模型就不多提了，现在最流行的数据模型了。

对于这两种模型，我的理解是Document Model是对写比较友好的数据模型，而Relational Model对读和Update比较友好。这里的友好指的是对数据的写入并不会做很多的限制以及处理以符合写入的需求。可以相对的符合数据原有的样子。 因为相应的应用场景也比较清楚了，Document Model适合那些对Join需求没那么高的场景，并且不会修改的场景，比如写入一个Log信息用于分析。而有Join场景的应用则主要考虑Relation Model的数据模型。

Query Language 接下来作者又谈了谈Query Language，虽然作者用了不少篇幅在阐述这块，主要的想表达就是一个思想，的SQL这种语言是一种High Level的声明式语言，从而可以留给数据库以优化的空间。

ch3 该章讨论的重点在于如何存储数据。本章刚开始讨论的是Key value的存储模型。对于存储数据而言，首先要考虑即两方面的问题。即读写性能。

*   对于写的性能其实就是解决如何减少随机写入，因为无论是机械硬盘还是SSD，现在顺序写入的速度都要远远高于随机写入的速度
*   对于读即采用何种方式组织数据，以便快速的获取数据。

对于把随机写转换为顺序写，最直接的思想就是，直接Append所有的写入数据。但这种方式会导致两个问题。

*   数据急剧增大，因为你不停的append，又没有删除的机制，数据有很快的增大
*   查找数据会很慢。

对于遇到的这些问题，引入两种机制来解决，1. 重写， 2 排序。 对于重写即对Key Value这种数据进行重写，因为很多key的值都是被更新过了。排序主要解决查找数据较慢的问题，并且你会发现重写的过程当中也会使用查找的功能。

结合起来就是LSM Tree的思想。LSMTree思想主要有两个方面

*   硬盘中存着一些排好序的数据块，定期Merge，实现重写以及维持排序的数据结构
*   内存中维护着一颗排序平衡树，比如AVL或者Red Black Tree，当平衡树大到一定规模时，直接写入到文件中。

LSM-Tree主要是从Writed的角度入手，考虑数据存储的问题。而现在应用最广的数据结构B+Tree则是从读的角度入手考虑问题的。 这里首先考虑一下如何读取数据最高效，最直接的答案肯定是Hash Table。但是可惜的是Hash Table如果在硬盘上实现会涉及到很多的随机读写，性能极差。出了hash table这种O(1)操作的数据结构。退而求其次则是O(logn)的操作，那么就是平衡二叉树。但是为了在硬盘上实现Tree，尽量减少随机的读写，那么就要在一个盘区存放更多的数据。那么就是B+树的思想。这里需要指出的是，所有的父节点不存放实际的数据，只有子节点才存放数据，主要还是为了减少寻址操作。

介绍完了B+Tree以及LSM Tree，那么接下来的问题就是两者的应用场景是什么。从上边的介绍也可以看出，两种数据结构分别是从两个角度来考虑问题。所以对于两种数据结构的比较，有个thumb of rule，那就是 B+ Tree读性能浩宇LSM Tree。 LSM Tree写性能好于 B+ Tree. 其实各种DB在实现这两种数据结构时都在对其进行优化操作，比如WAL,就是B+树为了优化写进行的操作。

OLTP&OLAP

ch4: 这章主要讲了一些编码方面的问题。对于要在网络上传输的数据首先遇到的问题就是编码的问题。毕竟网络并不认识各种代码。一般程序设计语言都会有相应的序列化的模块。但是因为种种问题并不推荐时间，比如，使用会限定在某一个语言，并不利于多语言的开发环境。又比如安全的问题，如果Hacker攻破了某一个Module，那么完全可以Hack进一些恶意的代码进去。 因此比较通用的编码方式比较合适。比如Json， CSV，XML。这三种编码对开发者都是比较友好的，但是对Performance并不是很友好，也就是说，性能优化方面有许多可以做的。所以为了解决这个问题，有了Thrift和Protocol buffer等协议，其相对于Json主要的优化在于优化了Attribute。 本章节还有相当大的篇幅在讨论向上下兼容性的问题，但是很多讨论的点太过细节，我觉的需要在实际中有相应的应用才会能理解更深入些。

part2 分布式数据 ch5: replication 复制数据应该是分布式数据系统一个首先需要考虑的问题。即如何从一个节点复制到另一个节点，会遇到哪些问题。 对于复制数据而言，一个首先遇到的问题就是Node的拓扑结构问题。

*   第一种就是无leader模型，即客户端可以随机访问其节点进行写入和读取，这种模型的优点在于其scalability特别好。可以极好的支持写入和读取，因为写入和读取会自动balance到各个节点。但是有个致命的缺点就是工程实现起来极其复杂，各种时序corner case问题需要进行考虑。因此这种架构在各种项目中并不常见。我个人理解是这种模型的困难在于其各个节点之间的共识上需要做大量的工作，如果有个leader存在的话，很多共识性的工作就可以以leader上的数据为主
*   leader-follower的结构，client写入会走leader，然后replication到各个follower。这种模型的的优缺点与上边的模型其实是相对的，优点就是实现起来相对简单。缺点就是所有的写入的压力都在leader上了。如果leader挂了，需要额外的机制保证数据成功写入。

因为以上的原因所以本章节阐述的主要就是leader-follow这种架构的分布式数据系统。这里对于leader-follow这种架构遇到的另一个问题就是数据如何同步去到各个节点，即通过同步的方式还是异步的方式同步过去。一般来说同步的方式实现起来最简单，但是很多实际系统中不可能实现。比如跨Geo的系统，同步起来耗时太久。所以Asynchronous这种同步方式在实际生产中或多或少的话都有被采用。

接下来作者有讨论了一些Node节点recovery的问题。节点Fail的情况分两类，leaderfailed了或者follower fail了。这两种情况需要分别考虑，对于leader fail的情形，其首先要解决的就是重新选择一个leader的机制，第二就是让这个新的leader同步到各个config上去。对于follower failed的机制那么需要在这个节点recovery后如何把数据同步过去。一般同步有几种方法

*   statement同步，比如说Mysql中同步用户的sql语句，但是这种时候经常出问题，比如如果sql中引用了Now()这个函数那么其实这个函数再主从当中的执行结果是不同的。
*   底层log的同步，这个同步肯定没问题。但是因为太底层了，对带宽的要求先不谈，也不好做一些优化和调整。比如主从版本不一致的话就会导致数据的错误。
*   row级别的同步。这个其实介与两者之间的一种同步方式，可以简单理解用SQL将需要修改的数据明确的写出来，传入到follower节点。因此这种其实比较常用。

对于Asynchronous模式的leader-follower系统，为了系统的scalability，通常需要写入到leader节点，读取的话走的是follower那么会遇到一些奇怪的问题。比如

*   read your own write： leader刚写入，但是却读取不到数据，因为写入的数据没有同步到读取的follower上去了。这样就需要一些额外的机制来解决这种问题，比如加时间戳，判断读取的时间是否是最新的。
*   Monotonic Reads：即一个用户读取数据时，发现数据不一致，也是因为数据打到了两个数据不一致的node上，强行让其读取同一个node可以解决这个问题。
*   Consistent Prefix Reads：对于数据分布在不同partition上，并且有依赖关系的数据，读取出来的数据也可能存在时序上的问题。比如一个事件发现的正常是A->B.A和B分别存在不同的partition上，那么user读取到数据则可能是B->A。因为不同的partition的同步并不能保证。

以上的问题主要是用于数据是异步同步，以及读取节点的不一致导致的。

除去单个leader-follower模式还有相对的multi-leader的模型。这种模式多应用在datacenter距离比较远，比如跨Geo的情形。这种模型很容易产生conflict，相对于leader-follower模式来说，multi-leader会在各个Geo上分别设置一个conflict的处理模块。而不会单独在每个node上进行冲突的处理。另外multi-leader可以并不是纯粹的全部replication的模式。也就是说可以某个geo上存一部分数据，这样也避免了冲突的发生。

除了multi-leader与leader-follower之外，还有leaderless的架构，这种架构并没有明确的leader，一般通过client多次写入来实现replication的功能。这里实际上是把一些leader的工作转换为client的工作了，所以client端要处理很多的corner case。 对于这种架构，一个关键的点就是在于如何保证读取到最新的数据，一般采用的是R+W>N来实现，即读取加写入的node数大于总node数。这里RWN仅仅是指导思想，实际工程处理时需要很多的问题解决。比如写入时没有到要求写入的数量，改怎么办，保留写入的数据还是删掉，该怎么删？

对于 leader-follower & multi-leader & leaderless 三种架构的分析： 一般来说leader-follower更加简单，包括实现以及理解上。但是avaiablity以及Scalability上的操作要比其它两种架构复杂一些。 其它的两种架构的优缺点与leader-follower的架构相反。

ch6 partition 这章主讲讲解了partition的相关的知识。partition的这个概念的引入其实很顺利成章，对于分布式系统而言，如果数据量的增长大到一个node装不下了。就要分到多个node上，那么对于每个node就可以认为其是一个partition。 本章其实一直在讨论的就是一个问题，那就是怎么进行partitiion。就是如何分的问题。而主要考虑点在于partition对于读写的影响。 如何分有以下二种方法

*   按照范围分，比如如果数据日期这个元素，每年作为一个partition。但这样会导致数据的不均衡，比如今年的业务暴涨，那么今年的partition中的数据量就会狂涨
*   Hash分法，就要进行partition的值进行hash进行，会一定上缓解这个问题，但是要注意使用consistent hash的方式进行处理。以便partition进行横向扩展。 这里以上的两种方法都没法解决的一个问题就是hot key的问题。本章作者认为hot key应该是应用层面进行解决，比如加一个random number，但是这样再读取的时候也要讲这个random number与client结合起来。

接下来作者比较大的篇幅讨论的二级索引的场景，因为只有primary index的partition系统，实际上就是根据primary index进行partition,如何把散落在各个partition的数据merge起来，就是二级索引该干的事。一般有两种考虑,分别是读友好和写友好。

*   各个partitiion分别建立各自的二级索引，但是这样会导致读取时要访问所有的结点，将所有的数据merge起来。这种很明显是写友好。
*   第二种是对同一个key的二级索引放在同一partition中，读取时不需要再访问所有的节点。但是写入时就需要多次写入不同结点，以便更新二级索引。特别是有多个二级索引的情形。

在数据partition完毕后，client怎么访问呢。作者提了三种方法，但是我认为现在主流用的主要两种。其实这三种完全是根据把路由信息存在哪里进行分类的，

*   route信息存node，每个node都存一份。各个node之间进行各种sync，这种要求你使用的这个data system带这种功能，比如redis的集群结构。然而很多系统是不带这种结构的
*   放第三方的service， 比如zookeeper,eureka.这种现在使用最为普调，也就是日常所说的服务发现
*   存客户端，这种单独使用其实很不靠谱。因为route信息一旦改变，client信息就过期了，你总不能指望client之间同步route信息。所以这种一般作为一种辅助手段，结合以上两种方法进行性能的提升。并不能单独使用。

ch7 Transactions

本章节主要讲述了事务的相关知识，我认为事务主要解决了数据的崩溃以及并发的问题，以简化应用层的开发复杂度。 说到数据系统，就不能不提大名鼎鼎的ACID。ACID当中Consistency比较特殊，其实并不是Transactions可以解决的问题，其实应该在应用层上进行解决。比如说两个账户保证转入和转出的一致性。

并发对于读方面的影响在于，由于处于性能考虑，并没有多少系统真正使用纯正的serialable的数据库，而是适当的允许并发以提高性能。这样引起一些并发方面的问题。

*   首先是读方面的问题，即脏读以及可重复读的问题。脏读产生的原因在于事务并没有作为一个单独的原子性的操作，其中间状态被其它事务读取到了。而可重复读的问题在于，虽然事务被当做了一个原子性操作，但是isolation并不彻底。读取到了其它的事务。为了避免相应的问题，最简单的方式肯定是加锁了，强行并行化。但是加锁实在很影响性能，所以数据库普遍采用MVCC 的方式进行处理，即记录下相应的版本变动，当读取时，只读取已经读取这个操作发生时已经commited的操作。
    
*   对于写的影响最明显的莫过于Lost UPDATE，也就是多个UPdate并发操作同一个数据的话，后边的数据会覆盖前边的数据。解决这个问题就是其实思路也无它，就三种1.去并发化，有些DB支持原子性的操作2.加锁，即显示加锁 3. 乐观锁，即监测到数据变化再交由用户处理。
    

Write Skew and phantom 是另外两个有意思的坑，其产生的原因正好相反，Write Skew是由于select后Write，而Phantom则是write后select，但是中间由于其它事务的一些操作是的相应的select结果出现了变化。比较典型的就是我们平常用outlook定会议室的情形，当我们定会议室时，比如A和B同时定某一个会议室，同时先select一下看下有没有空闲，都发现有空闲，那么就会同时定。这样就会导致同一个会议室被两个人定的情形。解决这个问题除了一些特殊的技巧外，比如文中提到Materializing conflicts，比较普世的方法还是串行化。

对于Serializability，要求每个执行的transacton都很小，要不然会block住其他的Transaction。Two-Phase Locking是一种加锁的实现串行化的方案。锁分两类Exclusive/Shared Locked,与我们平常所说的读写锁很像。有一点需要注意的是，由于幻读是由于没法加锁一些未曾出现的行引起的，所以这里引入了一种新的锁，next-key lock，给一定的范围加锁，从而实现避免幻读的产生。

以上提及的锁都是一些悲观锁，但是还有一种乐观锁，可以防止避免幻读。即 Serializable Snapshot Isolation (SSI)，这种锁就是在commit是判断自己的数据是否被更改过，如果更改过久abort。

ch9 consistency and Consensus 从consistency谈到了Linearizability问题，因为对于一个系统而言Linearizability会更加易于application层进行开发，否则你就面临着读取到过去数据的问题。文中的一句说说的很好，所谓linearizability就是让多份copy工作的想一个一样。

casuilty （待补充）

Distributed Translation:讲的比较浅，说了说2PC，3PC。其实比较常用的TCC之类的都没有提及 Consensus 共识这部分我比较熟悉，没发现比较新的知识点

ch10: Batch Process 本章主要围绕着Map-Reduce框架讲解了一些Batch Process的常见处理Pattern。Map-Reduce作为Google的三驾马车之一，现在几乎已经从业者的一个常识了。 这里简单介绍一下Map-Reduce的思想，对于大数据量的处理，很简单的一种思路就是加机器进行处理，比如假如有10TB的数据，那么搞10台VM，一台处理1TB。这样解决了数据处理的问题，也就是Map-Reduce中Map的由来，但是有一个问题没有解决，因为很多数据是一个整体，相互之间有相应的relationship，Map-Reduce中，通过Map的操作将相应有联系的机器Reduce到一台机器上，以便让相互有联系的数据可以一起进行处理。 该章剩下大多数的章节都是对一些map-reduce的各种不同的case的介绍，比如如果一个key过大怎么办，map side join。接下来的一部分与我现在工作或多或少有点关系，我关注了一下。首先map-reduce有个缺点，那就是map-reduce必须是一个整体，换句话说，如果你的计算要经过多个map-reduce，那么你的计算过程会经过input-output-input-output…. 等等，这里的input到output的转换相当消耗计算资源，所以Spark等计算框架将一个task作为一个任务，把这些input output全部联系起来大大加快的计算速度，反之带来的一个小缺点就是如果中间某个task计算失败了，就必须要进行recompute了
