---
title: "Improve performance on HBase"
date: "2018-04-17"
description: "This blog will tell you how to improve read performance on HBase"
categories:
    - "Hadoop"
    - "Cloud computing"
    - "HBase"
---

# Tips on improving reading performance

In some scenario, we need to have high RPS in HBase reading.Therefore, we could change some configuration in HBase to meet this requirement. I found this [blog](http://blog.asquareb.com/blog/2014/11/21/leverage-hbase-cache-and-improve-read-performance/) very helpful. HBase official [guide](http://hbase.apache.org/0.94/book/perf.reading.html) is also great.


- Increase `hfile.block.cache.size` and decrease `hbase.regionserver.global.memstore.size`