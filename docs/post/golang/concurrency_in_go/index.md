
ch1~2 主要是引论以及CSP一些知识的介绍

ch3 go语言并发的一些库的应用。 包括 waitgroup,sync, syncrw, cond, pool, once channel, select总体介绍的比较基础. 有个知识点比较有意思，我们知道once.do只执行一次,但这里的一次并不是指的根据func的名字来确定的,而是指的do这个函数执行一次。举个例子来说 once.do(func1), once.do(func2) 这其实算执行了两次

ch4 一些并发的concurrency pattern。 刚开始提到的confinement这种控制并发的编程模式感觉比较tricky，有时也不好想到，本质应该是通过设计将并发修改的情况取消。 方式goroutine leak这里提到的一个设计原则很关键，就是谁创建，谁销毁，这样就不会有泄露的问题了。 or_这种方式如果不考虑不知道数量的情形下的话，也就是一个select的语法糖式的写法。
ch5 主要讲了一些并发使用场景，比如heartbeat以及如何cancel掉一个goroutine。这章其实是一些零星的知识点的组合，很多点其实都不能算并发的问题，比如Ratelimiter，其实是个限制并发的module。

ch6 GMP梳理了一下，对这块之前专门学过。所以这章没细看，大概扫了了下。
<!--more-->
