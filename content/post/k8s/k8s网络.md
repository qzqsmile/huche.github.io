---
title: "K8s网络"
date: 2023-12-08T23:10:22+08:00
lastmod: 2023-09-27T23:10:22+08:00
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
零星知识点：
1. linux下 route命令会列出一系列的路由list。有匹配地址的走匹配地址，没有的走网关。如果都没有就报错。网关默认如果配置多个，只有一个生效。

{{< figure src="/images/net/route.png" alt="Image Alt Text" title="" >}}
