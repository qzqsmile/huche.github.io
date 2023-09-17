---
title: "Programming Kubernetes notes"
date: 2021-01-17T23:26:11+08:00
lastmod: 2023-09-27T23:26:11+08:00
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
ch1 主要是一些overview式的介绍。如果你对k8s之前比较熟悉的话，很多内容都是重复的。对我而言，有以下几点有些启发。

- 本书提名k8s编程，这里与其它运行在k8s里的container一个重要的区别是，应用是否自己知道运行在k8s里。换句话就是是否会使用一些k8s的API
- K8s采取的是乐观锁的机制，Controller需要自己实现乐观锁的判断逻辑
- K8s采取Edge-driven和level-driven两种结合的方式，来实现响应时间，以及稳定性的要求
- Operator 本质是为了帮助一些复杂的stateful的container进行部署的模块。
  
ch2 主要讲了API Server的相关知识，对我没什么特别新的东西。唯一学到的是k8s API。分为GVR(Group Version Resource)GVK (Group version kind)。区别也很简单，简单来说就是类和实例之间的关系。 GVK可以对应许多GVR

<!--more-->
