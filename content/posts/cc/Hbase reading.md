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


- Increase `hfile.block.cache.size` and decrease `hbase.regionserver.global.memstore.size`. The first configuration is for reading cache and the second one is writing cache. The sum of two value should be 0.8
- Decrease `BLOCKSIZE` in HBase table. When we get one block and save it in the cache, if we did not read it in the future, the rest of block is wasted. Therefore, we are supposed to reduce blocksize from 64M to an appropriate value.
- Increase `hbase.regionserver.handler.count` to handler more threads requests.
- Split data and make sure each region server gets similar amount requests.
