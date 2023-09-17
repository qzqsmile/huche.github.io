

zookeeper可以简单认为是一个KV系统，它与其它的一些存储组件。比如 Mysql， Redis 集群的区别。个人认为主要集中在以下几个方面

由于Zab这个multi-paxoes协议的保证，zk的集群是高可靠的，换句话说是满足CAP中的CP。其它的比如Redis集群以及Mysql主从或多或少都有些问题。比如Redis集群应该是只是AP，Mysql亦然
提供了自增结点这种数据结构，自增这种操作方式很容易的区分两个操作的时间关系。可以快捷的实现一些分布式功能，比如FIFO的分布式队列。以及分布式锁中也有应用
提供了Watch这种消息推送的消息传递机制，比单纯的客户端轮询消息效率要高不少。
提供了临时结点这个数据结构，当连接断开后自动删除。可以监控客户端的进程的状态，防止其崩溃

分布式队列
(https://github.com/qzqsmile/books/tree/master/zookeeper/distributedQueue)

分布式锁
https://github.com/qzqsmile/books/tree/master/zookeeper/d_lock

服务发现
https://github.com/qzqsmile/books/blob/master/zookeeper/serverDiscovered/serviceDiscovered.go

分布式消息
todo

配置中心
todo

负载均衡
todo

zk的watch机制

<!--more-->
