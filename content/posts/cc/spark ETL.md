---
title: "Spark for ETL"
date: "2018-03-27"
description: "This blog will tell you how to perform ETL on Json file using pySpark."
categories:
    - "Spark"
    - "Cloud computing"
    - "Tips"
---

There is not much information about how could we perform complex operation on pySpark. I summary some tips from stackoverflow and my experience. Hope it will be helpful for you.

# How to Read JSON to DataFrame

There are serveral ways to read json in Spark. The most common way is to load json in a DataFrame. Why not rdd? Because json contains some hierachy information and it could not represent well in RDD. We could use `df.printSchema()` to see json file schema.

```python
from pyspark.sql import SQLContext
sqlContext = SQLContext(sc)

df = sqlContext.read.json("demo.json")

df.printSchema()

# a
#--b
#--c
#-----d

```
# How to Select Nested entities from DataFrame

It is very easy to select element from DataFrame to extract important elements, as well as nested entities. We could use `a.b` to get that nested one.

```python
df = df.select("a.b","a.c.d")

# we could rename col in select
df = df.select("a.b",col("a.c.d").alias("new_d")）
```

# How to filter in Dataframe 

Fileter is a very powerful function in Spark, we not only could filter word, but also filter length of words and filter elements from a list.

```python
df = df.filter(size(col("a")) > 0)
# check column c value in list ["a","b"]
df = df.filter(col("c").isin(["a","b"]))
```

# How to Drop NA

It is also very easy for us to drop NA value in DataFrame. We can drop a list of subsets in DataFrame.

```python
df.na.drop(subset=["a","b"])
```

# How to change Dataframe to RDD

When we get a RDD format schema, we could use `rdd= df.rdd` to change DataFrame to RDD. RDD has some function that DataFrame does not provide, like `groupByKey`,`reduceByKey`.

# How to perform array concat with ReduceByKey

As we know, we could use ReduceByKey to count number, like word count. However, it is not easy to concat value into a list. 

When we have one record (a,1) and another (a,1). We would like to have (a,[1,1]). We could use the following code.

```python
rdd.map(lambda x:x[0],[x[1]])
rdd.reduceByKey(schema2.reduceByKey(lambda x, y: (x[0] + y[0]))
```


# Save RDD to csv file

We could use map to get a whole line string with all columns data for one line. Then we use saveAsTextFile to save.

```python
def toCSVLine2(data):
    return ','.join(str(d) for d in data)


rdd.map(toCSVLine2).saveAsTextFile("xxxx")
```

# How to sortByKey

We could use `sortByKey` and `sortBy` to sort, but we need to pay attention to parameters. 

- `sortByKey(self, ascending=True, numPartitions=None, keyfunc=lambda x: x)` 
- `sortBy(self, keyfunc, ascending=True, numPartitions=None)`


Here, what is numPartitions? It will do sort in the number of partition, which will increase sort speed.

```python
>>> tmp = [('a', 1), ('b', 2), ('1', 3), ('d', 4), ('2', 5)]
>>> sc.parallelize(tmp).sortByKey().first()
('1', 3)
>>> sc.parallelize(tmp).sortByKey(True, 1).collect()
[('1', 3), ('2', 5), ('a', 1), ('b', 2), ('d', 4)]
>>> sc.parallelize(tmp).sortByKey(True, 2).collect()
[('1', 3), ('2', 5), ('a', 1), ('b', 2), ('d', 4)]
>>> tmp2 = [('Mary', 1), ('had', 2), ('a', 3), ('little', 4), ('lamb', 5)]
>>> tmp2.extend([('whose', 6), ('fleece', 7), ('was', 8), ('white', 9)])
>>> sc.parallelize(tmp2).sortByKey(True, 3, keyfunc=lambda k: k.lower()).collect()
[('a', 3), ('fleece', 7), ('had', 2), ('lamb', 5),...('white', 9), ('whose', 6)]

>>> tmp = [('a', 1), ('b', 2), ('1', 3), ('d', 4), ('2', 5)]
>>> sc.parallelize(tmp).sortBy(lambda x: x[0]).collect()
[('1', 3), ('2', 5), ('a', 1), ('b', 2), ('d', 4)]
>>> sc.parallelize(tmp).sortBy(lambda x: x[1]).collect()
[('a', 1), ('b', 2), ('1', 3), ('d', 4), ('2', 5)]
```