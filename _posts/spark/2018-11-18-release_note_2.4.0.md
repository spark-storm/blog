---
title:  "Spark 2.4.0主要变更"
categories: [Spark]
tags: [Spark, release_note]
date: 2018-11-18 20:41:00
layout: post
published: true
---
原文:https://spark.apache.org/releases/spark-release-2-4-0.html

# 概述
2.4.0是2.x的第五个版本, 这个版本更好得整合了为深度学习设计的的Barrier Execution Mode;增加了30多个处理复杂数据类型的的高阶内置函数;增强了k8s的集成;
支持了Scala2.12;支持了Avro作为数据源; 支持图片数据源;更灵活的流式数据提取;去除了2GB块大小的限制;增强Pandas UDF;还解决了其他1100个问题.

# Core and Spark SQL

## 主要特征
* Barrier Execution Mode:这个特性是为了更好得集成深度学习框架的.在用Spark来机器学习的场景中,经常会先处理预处理好数据, 然后将其喂给训练框架,训练出来的模型可能又需要再次处理,Spark目前的机器库是基于RDD实现的, 但是现在深度学习大热的情况下,兼容深度学习是一个趋势,而TensorFlow是用C++和Python实现的,这就涉及到异构的问题了, 为了整合进来,将深度学习作为当中的stage,复用Spark现有调度和失败恢复机制,能这样做的前提是[Hadoop支持调度GPU资源](https://hadoop.apache.org/docs/r3.1.0/hadoop-yarn/hadoop-yarn-site/UsingGpus.html).我们团队深度学习现在的使用姿势是先用Spark处理好训练样本,然后将样本落盘,TensorFlow再去将数据拉下来,在GPU机器上训练,处理好之后就产生模型,产出的模型和embeding向量再由Spark去处理, 这些任务的调度是在外面做掉的.用了这个特性之后,理论上更方便了,但是Hadoop是在3.1.0才支持GPU资源调度的,生产环境目前来说大部分还是2.x吧, 还有一个问题GPU机器是比较贵的,如果将GPU机器作为Hadoop的计算节点是不是有点浪费了?
* 实验性得支持了2.12,Spark在3.0将使用这个版本, Scala的版本之间的兼容性够蛋疼的,这也是我不太喜欢scala的一个原因.
* 增加了高阶函数, 包括一些日期 位运算 数组操作和map的操作, 之前自己写udf实现的数组处理操作有不少已经支持,内置的使用更方便,性能也更好点. 这个issue介绍了这次新增的[SPARK-23899](https://issues.apache.org/jira/browse/SPARK-23899)
  
* 内置集成了Avro数据源, 主要是使用上更方便了.


## API
* [SPARK-24035](https://issues.apache.org/jira/browse/SPARK-24035) Sql语法支持Pivot
* [SPARK-24940](https://issues.apache.org/jira/browse/SPARK-24940) Sql支持Repartition和Coalesce
  [SPARK-19602](https://issues.apache.org/jira/browse/SPARK-19602) 列名支持限定名,适用于查多库的情况.
  [SPARK-21274](https://issues.apache.org/jira/browse/SPARK-21274) 实现了 EXCEPT ALL 和 INTERSECT ALL

## 其他
* [[SPARK-23146]](https://issues.apache.org/jira/browse/SPARK-23146) k8s作为资源管理器的情况支持了client mode
* [[SPARK-22666]](https://issues.apache.org/jira/browse/SPARK-22666) 数据源支持图片,该特性的代码mllib包里面,使用的时候需要引入.




