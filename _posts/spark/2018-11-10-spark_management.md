---
title:  "Spark集群管理器"
categories: [spark]
tags: [spark, yarn, mesos]
date: 2018-11-10 08:29:00
layout: post
published: true
---

在之前的[大数据架构背景介绍]({% post_url 2018-10-28-framework %})中介绍过, 我们这里的的Spark应用都是跑在Yarn上的. 所有选择Spark on YARN方式部署是顺其自然的一件事, 不需要引入其他多余的依赖, Spark提供了多种集群管理的实现, 对比起来各有千秋, 当每种方案都能满足核心需求的时候, 选择现成的是一件比较省心的事情. 

Spark支持的集群管理有[Standalone Mode](https://spark.apache.org/docs/latest/spark-standalone.html)、[Spark on Mesos](https://spark.apache.org/docs/latest/running-on-mesos.html)、 [Spark on YARN](https://spark.apache.org/docs/latest/running-on-yarn.html)、[Spark on Kubernetes](https://spark.apache.org/docs/latest/running-on-kubernetes.html)、 单机调试的local模式。

网上很多它们几个的[对比文章](https://www.google.com/search?q=spark+yarn+mesos+standalone+kubernetes+difference) 下面说说我理解的各个集群管理器的差异
1. Standalone Mode， 是Spark自带的资源管理器.优点是无需其他外部依赖(高可用需要用zk来保证), 缺点是升级Spark版本的同时需要升级集群.客户端和服务端大版本要一致.
2. Spark on Mesos, Mesos最当初就是为了Spark设计的, 提供了粗粒度(Coarse-Grained)和细粒度(Fine-Grained)的资源管理方式, 但是考虑到Spark已经支持[动态资源分配(Dynamic Allocation)](https://spark.apache.org/docs/latest/job-scheduling.html#dynamic-resource-allocation)了, Spark2.0.0之后已经移除了Spark on Mesos细粒度的特性了 [Remove Mesos fine-grained mode subject to discussions](https://issues.apache.org/jira/browse/SPARK-11857)
3. Spark on Yarn, Yarn是集成在Hadoop里面的, 在Hadoop2.0之后, Hadoop就将计算框架和资源管理分离了, 使得很多应用可以直接基于Yarn上跑, 所以 Spark的不同版本在yarn上跑的时候也不用像Standalone的时候需要考虑版本对的上, 这个好处在现在Spark更新迭代这么频繁的时候特别好用. Yarn一直被人诟病的一个点是只支持粗粒的资源管理, 细粒度的正在讨论中[Support changing resources of an allocated container](https://issues.apache.org/jira/browse/YARN-1197), 但是对于现在支持动态资源配置的Spark来说,这个特性显得反而是鸡肋了.
4. Spark on Kubernetes, Kubernetes是Docker资源管理器, 这一实现主要利用Docker弹性部署的特性, 同时资源和环境隔离程度更高, 这一方案是2.3的新特性,是一个实验性的方案, 不支持client的部署方式, 在最近发布的2.4.0中已经[支持](https://issues.apache.org/jira/browse/SPARK-23146)了.个人感觉这个将是未来发展的方向, 这种方式借助docker的特征, 隔离性高, 也容易搞弹性部署, Spark也在往这方面靠.

从以上的对比来看, 个人感觉on yarn的方式在现阶段来说是比较成熟可靠的, 大数据的很多组件都有on yarn的实现, 公司选择的话, 还得考虑人员对各类组件的熟悉程度以及机器成本的开销.

