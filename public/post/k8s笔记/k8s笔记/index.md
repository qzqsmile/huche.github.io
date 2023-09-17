

这里主要记录一下学习K8s的一些简单概念以及操作。想到哪写到哪

kubeadm 集群的安装
-------------

kubeadm的出现大大简化了K8s集群搭建的难度，但是这里最坑的一点就是国内把很多安装源给block了。所以利用kubeadm按照k8s官方教程来安装其实是行不通的。这里我鼓弄了一天才搞好，主要的精力都花在各种替换软件源上边了。 这里简单记录下需要替换的源

1.  替换ubuntu的软件源 [https://mirror.tuna.tsinghua.edu.cn/help/ubuntu/](https://mirror.tuna.tsinghua.edu.cn/help/ubuntu/) ubuntu的软件源其实没有被block，但是出于速度的考虑，还是替换下比较好。
2.  安装docker&替换docker的软件源 [https://www.cnblogs.com/LUA123/p/11401962.html](https://www.cnblogs.com/LUA123/p/11401962.html) 我这边使用docker pull 镜像有点问题。换了阿里的源就好了
3.  安装kubeadm [https://www.jianshu.com/p/9540f8e3be34](https://www.jianshu.com/p/9540f8e3be34) 这里对源key的操作值得注意，即执行 curl -s [https://mirrors.aliyun.com/kubernetes/apt/doc/apt-key.gpg](https://mirrors.aliyun.com/kubernetes/apt/doc/apt-key.gpg) | sudo apt-key add -
4.  最后的安装步骤可以参照zhihu的这篇，最后再在各个node上执行下kubeadm join命令即可 [https://zhuanlan.zhihu.com/p/46341911](https://zhuanlan.zhihu.com/p/46341911)

Pod
---

    Pods是K8s调度的最小单元， 这里经常会有疑问的是，为什么我们需要Pod，而不是以container作为最小的调度单位。主要原因有两点：
    1. 因为某些程序需要多个进程进行协同进行功能，换一句话说就是某些program需要多进程来实现。但是container的设计初衷则是就是为了单进程的。如果在container中强行利用fork实现多进程，对于fork出来的进程其实是没法进行管理的。（这里略有疑惑）
    2. 对于Pod而言，利用一个local的一个小的container，可以实现同一个pod中的所有container。共享一些资源，比如network等等。例如，对于Pod而言也有共享同一个IP地址。
    label 
    功能如其名，给pods打标签。主要是方便后序的管理
    annoation
    注释，主要是解释说明的作用,与label的区别主要是不能进行后期的管理，并且一个内容较多
    namespace:
    类似于语法中的概念，同一个namespace只能看到本namespace里的内容。但是这里要注意，不同的pods并不是真的进行了隔离。如果你知道另一个namespace下的POd的Ip地址，依然可以进行通信。
    

常用命令 kubectl get pod kubectl get pod -o wide kubectl describe pod + resource name

kubectl exec -it memory-demo -c memory-demo-ctr-2 bash 在特定的pod的特定容器内运行bash

replication, probe
------------------

probe主要是为了监测pod是否挂掉的模块，如果挂掉了。就自动重启。但是这里如果node挂掉的话，probe其实是不起作用的。这里也是与replication的区别，node挂掉，replication也起作用。 replication vs replicationset vs daemonset replication保证pods一定的数量在running，或多或少的话。都会减少或者增加。set与其相比更好，因为selector更丰富，一般来说总是优先选择replicationset。daemonset则保证每个node上执行一个pod

Service
-------

用于辅助Pods与外界通信的moudle，因为Pods默认是无法连接外网的。

label:
------

功能如其名，给pods打标签。主要是方便后序的管理

annotation
----------

注释，主要是解释说明的作用

deployment
----------

K8s的Pod虽然是最小的单位，但是只有创建和删除这两种操作，使的其在生产环境中使用的不是很多。生产环境更多的是使用Deployment这种操作。Deployment可以更方便的部署和Scale container

kubectl create deployment NAME –image=image 直接创建以这个deployment，不用yaml

Namespace
---------

进行资源隔离的一个概念

常用命令 kubectl get namespace kubectl -n namespace get pod 列出指定namespace下所有的Pod。 kubectl apply -f xx.yaml 创建+更新 kubectl delele -f xx.yaml 删除相应的资源

kubctel create + rescour type 创建相应的资源

默认的有三个 default: 默认的都在这里 kube-public：此命令空间可以被所有人访问 kube-system：包含了许多的k8s默认的系统资源

Deamonset
---------

每个nodes上都会分配一个Pod。

tolerations: # this toleration is to have the daemonset runnable on master nodes # remove it if your masters can’t run pods - key: node-role.kubernetes.io/master effect: NoSchedule

这里要注意污点的影响

Liveness & Readiness
--------------------

有三种Liveness，

1.  ExecAction: 执行特定的命令，成功则退出码为0
2.  TCPSocketAction， 执行TCP检查固定的端口
3.  HTTPGetAction 执行HTTP检查固定的端口 Liveness检查Pods是否是活跃的，不是的话，尝试重启。 readinessProbe 则是在Pod中的容器启动完成后，就认为Pod中的应用程序可以对外提供服务。该Pod就会加入到对应的Service

volume
------

为了各种Pod之间共享数据。Volume有两个基本的概念PV PVC 其中PV是给管理员使用的，用于指定数据具体存放在什么地方。如果用公有云部署，那么一般都会提供相应的存储系统，比如Azure的Azurefile。 PVC则是用户使用的。其中StorageClass需要与PV相同，用于绑定相应的PV。 一般的绑定逻辑是POD—>PVC->PV

HostPath: 存放在Node上的类型 EmptyDir： 用于多个Container之间共享的类型

ConfigMap&Secrets
-----------------

用于存放程序配置的component.其中Secrets中会Base64加密

疑问点
---

Service与Pod之间的mapping关系,如何创建一个nodeport

1.  k8s为何呀swap off 主要是想不到一定要swap off的场景，就没设计进去。默认没有swapoff可以简化设计。

secret为什么是安全的
-------------

我之前看k8s secret一直很好奇，为啥base64的算法也能称之为加密算法。这除了人不能肉眼读取之外，与configmap有啥区别。最近又查了下相关的知识，总结一下。

1.  最新k8s已经能实现不是base64的加密解密
2.  虽然用base64编码，但还是实现了些安全方面的工作。[link](https://blog.csdn.net/kunyus/article/details/94392696)

k8s in action
-------------

chapter12: 这章主要讲的是API的RBAC控制策略。对于经典的RBAC都有三种对象，分别是Role, User, Permession。这里对应K8s中相应的概念是 Role: Role(适用于namespace), ClusterRole(适用于cluster) User: ServiceAccount(大多数情形) Permession: Action (比如 get, list，etc) k8s中相对于经典的RBAC有一点不一样的地方即是多了一个RoleBinding的对象作为Role和User之间连接器。这里我没想明白为什么要多出binding这个对象进行连接的操作，感觉上直接在User或者Role Refer所适用的User应该也可以.

chapter13: 这种主要讲的是对于Pod的一些安全性配置。防止如果某些Pod被侵入，权限过大。以及一些k8s自己的component如何实现Node的管理的功能。

chatper14: 对计算资源的控制。一般有CPU，以及Memory两种。 请求的参数主要限制在Request以及Limit两个上，分别是请求资源的上限以及下限。超过上限了就是kill掉。这就引出了该kill那个pod的问题了，根据request以及limit的配置，可以将Pod分成三种Besteffort, Burstable, Guaranteed,优先级从低到高。 另外这里有个隐含的配置，K8s的SwapOff都是默认关闭的。K8s作为一个Service部署的系统，其部署的Service一般不会超过Node的内存总限制，而且开启SwapOff后对Docker的性能又有严重的影响。所以K8s默认不开SwapOff,如果内存超过limit了，那么就显示的报出错误，让开发人员进行fix bug或者调整Node的配置。而不是采取SwapOff的手段隐藏这个Risk.

chapter15: AutoScale是本章主题。主要介绍了Pod的三个方面的参数进行AutoScale CPU, Memory, QPS.具体是一些详细参数的配置。 有一点比较让我感兴趣的是，对于使用一些云计算提供商的计算服务，其可以实现AutoScale Node的功能。

chapter16 一些node的taint以及affinity之类的选项。主要作用是控制Pod的调度，具体估计要实际应用才能真正理解

chapter17 一些k8s开发的最佳实践，其中Pod的shutdown流程可以了解一些。K8s先发送SigTerm给container，使其最好可以优雅的结束，如果不行的话，再发送sigkill的信号给container，强行shutdown。

chapter18 本章主要讲了CRD以及Service Catalog。其中CRD主要是用户自定义的一种类型，定义起来比较方便，但是编写controller会比较麻烦。其Controller的主要原理就是监听API Server的变化，所有的变化进入一个队列当中进行处理。 (Service Catalog待填坑，只知道是实现pod连接外面请求的)

