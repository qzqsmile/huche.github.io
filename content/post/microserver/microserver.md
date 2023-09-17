---
title: "Microserver"
date: 2020-08-01T23:19:08+08:00
lastmod: 2023-09-19T23:19:08+08:00
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

微服务的架构

以前应用都是比较小的规模，可以单体开发。但是当规模上来以后，不同的人负责不同的模块，这时相互之间的耦合会很复杂，如果仍采用单体应用的开发模式，很容易造成module之间的互相影响。 比如说，网站的支付系统挂了，会导致整个网站不能访问这种情况。因此此时就引入了微服务的开发模式。其中微服务大白话来说就是，将单体应用中的相互模块之间的调用变为RPC之间的调用。而各个module分别坐落于不同的机器上。 微服务这门技术讨论的就是如何使的这种调用更加可靠，高效.

1.微服务注册

{{< figure src="/images/arch/microservice_1.png" alt="Image Alt Text" title="" >}}

对于微服务而言，首先要解决的问题即是微服务注册的问题。如图所以客户端在访问服务端前会先让注册中心访问所需要的的地址。