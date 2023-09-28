
最近因为业务需要，尝试搭建着一些大数据平台。发现相对于纯手工从头搞起，还是使用Azure上的Service来搭建来的香。


以上是Azure推荐的的BI数据仓库的搭建图，这里说下我对其中一些基本模块的理解

1. Data factory
作为一个ETL的Service，本身其不存储任何数据。其中EL是通过Pipeline+activity这种方式进行实现。而T则是利用DataFlow这部分来进行实现，如果你详细看过DataFlow输出文件就会发现，其底层应该是利用Spark进行实现的。DataFlow可以简单的认为是个Spark可视化编程的Tool 在把玩了一番Data factory后，觉的其有两点非常吸引人。
Code Free 不用写代码，或者说写很少的代码就可以实现整个的ETL的过程，这就意味着很多要在代码里处理的corner case它都帮你处理了，势必对系统的 Reliability有很大的提升。
与Azure Service的深度整合。包括 Machine learning， Azure DataBrick，Azure Function等等。在Activity里都能找到相应的module。
2. Data Lake Storage Gen2
没有接触过hive之类的文件系统，不知道与其有什么不同。但Azure Data Lake 感觉就是一个支持大数据存储的文件系统。用法等同于我们像PC存一个文件的方式一样

3. Azure Synapse Analytics
这部分我看了好久后，才发现其实分两个版本，而且两个版本的差异还挺大。分别是formerly SQL以及Workspace, 只能说微软爸爸真会玩。 我一直看的是formerly SQL这个版本，我认为其也就是一个Data Warehouse + Scale SQL computing power。 也就是一个数仓加上一个弹性的SQL运算模块

4. Azure Analysis services
这块本来是我看的最懵逼的地方，文档写的云里雾绕的。感觉各种高大上，但是就是不知道觉的了什么实际问题，后来还是看看youtube的视频才弄明白。 简单来说，它其实原本是包含在PowerBI service里边的，这里把它提取出来Deploy到Azure上，那么就可以方便的Scale Out。这里就将一个解决的痛点问题：PowerBI文件的 1GB限制就是这个Service进行限制的。如果你部署到Azure上可以轻松突破1GB的限制。

5. Azure DataBrick 我的理解就是部署在Azure上的Spark计算框架。

6. PowerBI 这里不谈对PowerBI的理解。因为上边说了那么多其它的Service，其实最复杂的就是PowerBI了，其强大不是一两句话能说的清的。 前段时间看了个统计最喜爱的BI产品，PowerBI名列第一。之前觉得PowerBI操作简单，搞起来显的跟糊Excel表一样，完全没有撸代码的那种成就感。但是反过来想这样才是一个好的产品。不得不说MS做软件的功力还是深厚。