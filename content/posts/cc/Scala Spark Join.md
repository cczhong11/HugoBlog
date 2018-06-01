---
title: "Scala join RDD tips"
date: "2018-04-21"
description: "This blog will tell you how to accelerate join performance on rdd"
categories:
    - "Soark"
    - "Cloud computing"
    - "Scala"
---

# Why Spark RDD join is expensive

As we know, Join is the most expensive operation on rdd. The reason is when we join two rdd, it requires corresponding keys from each RDD are located at the same partition so that they can be combined locally. Therefore, we will shuffle keys from each rdd to make sure they are in the same partition. 

![](https://www.safaribooksonline.com/library/view/high-performance-spark/9781491943199/assets/hpsp_0401.png)

You could learn more about rdd join from [here](https://www.safaribooksonline.com/library/view/high-performance-spark/9781491943199/ch04.html)

# What we should do

If these two rdds have the same key, we could partition their key in the first place with the same partitioner and then we could reduce shuffle part in `join`, which will save us lots of time.

We could use the following code in scala to do this thing.

```scala
import org.apache.spark.HashPartitioner

val partitioner = new HashPartitioner(N)

val rddA = xxx.partitionBy(partitioner)
val rddB = xxx.partitionBy(partitioner)
rddA.join(rddB)

```

# How many partitions we should choose?

My suggestion is to choose a number that will give each executor 2~3 tasks. For example, if you have 20 executors in total, you could choose 40 as the number of partition. 