
总结一些中k8s的架构各个部分的功能


{{< figure src="/images/arch/k8s_1.png" alt="Image Alt Text" title="" >}}

总体结构

etcd: 分布式存储的module，主要提供高可靠存储。其中我们使用kubectl编写的一些yaml文件，其中的描述信息都会存取其中
API Server: 与Etcd打交道的的模块，一切访问ectd的操作都要经过它 Scheduler：简单来讲，就是为Pod选定Node的模块，因为调度时需要考虑一些设定的规则，比如node-selector等。就是在这个部分实现的
Control-Manager: 监控ectd的yaml文件是否变化，并采取相应的操作。不同的模块其实对应不同的control-manager，比如deployment对应自己的controller，因为不同的某块需要的manger策略其实不相同。
kube-proxy: 一个proxy，之前通信都是用经过iptables->kubeproxy->pods 这样。但是后来其实不用经过kubeproxy了。
kubelet: 各个node里直接操作container的module
如下这个kubelet到创建contrainer的流程也很有参考意义

{{< figure src="/images/arch/k8s_2.png" alt="Image Alt Text" title="" >}}


<!--more-->
