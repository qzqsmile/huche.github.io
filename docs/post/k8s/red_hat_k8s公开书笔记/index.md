
<!--more-->


Red Hat k8s 公开书的读书笔记

主要总结PartII,因为Part I都比较基础。感觉这书有点笔记向，所谓的笔记向就是像作者自己做的笔记。 这种书的一大特点就是知识点很多都是总结的，如果你没有实际相关的使用经验其实很难理解，但是如果你自己搞过相应的模块，又觉得通篇没啥新的内容。个人不是很喜欢这种类型的书.

1. Batch Job Problem: 有些程序只需要跑一次，不需要长期的运行，提供一种Service的服务 Solution: 使用K8s里边Kind:Job

2. Periodic Job Problem: 需要周期性运行的程序，比如说采集数据的Pod Solution: 使用K8s里边Kind:CronJob。在Yaml文件里可以设置相应Periodic的周期。

3. Deamon Service Porblem: 与Deamon Process概念类似，守护Service。 Solution: 是用DeamonSet的Kind。这里可以想到的是，为了保证 Deamon Service的 reliability。最好的方法就是每个Node上运行一个Pod，实际DeamonSet也是这么实现的

4. signleton service 单列模式，其实总结起来就是一句话。对于K8s而言，要求不严格的单列模式使用replicaset，并设置replica=1(因为某些特殊时刻可能不是单列)。严格的话就要上stateful

5. stateful service 要使其成为stateful service。其本质就是使的pod的名字唯一，再重新launch的情形下也是。

6. service discovery 总结了下集中service的主要用法。

Structural Patterns 以下四种都是一种组合方式的K8s Desgin Pattern。主要思路都是在main container外组合另一个 container 用以实现更为强大的功能。

1. Init Container Problem: 在主Container启动之前需要进行一些初始化和设置 Solution: Init Container会保证这样的Init会一个接一个的进行执行。
2. Side Car Problem: main Container需要与其它的一些Container进行组合 Solution: 主Main Container外另起一个Container，组成一个Pod。用以增强main Container的功能。

3.   Adapter Problem: Main Contaienr有的数据或接口不一致 Solution: 封装一个Adapter的 Container用以作接口转换

4.  Ambassor Problem: Main Container需要访问外界的Service。但是又不能直接访问，比如需要Auth认证之类的情形 Solution: 使用Ambassor，其实就是一种Proxy模式了。这里我觉的与Adapter的不用在于，Ambassor是访问其他module。而Adapter是提供一个接口供其它moudle进行访问。

5.  Env Configuration 最low的一种config配置模式，本质就是设置env的环境变量
    
6.  configuration resource 相对不low的一种，利用k8s的 configmap以及secret的component。 这章关于secret的到底secret在哪里还有点意思。之前我也一直纳闷，搞个base64就敢称自己为secret，好歹搞一点实实在在的加密算法呀。这里讲了以下三点，为啥它被称为secret
- 只有需要secret的node才能访问secret
- 不写入硬盘，如果pod被remove了就没有了
- etcd里它的存储是加密的

7. Immutable Configuration 将一些config封装到一个image里，然后通过init container的方式被其它的container引用，这里我有点疑惑，如果main container修改了 config image，那岂不是不是immutable的了。我只能猜测作者的意思是image里的config如果每次都是重新拉取应该不会变。 不过话说回来，个人感觉这种方式封装config过于复杂了，获的好处也有限。一般情况下k8s自带的config应该可以满足需求了。

8. Configuration Template 通过一些configuration template来简化配置的技术。细节没看懂，估计要有实际应用经验才能理解透彻一些。

