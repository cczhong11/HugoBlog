---
title: "Mapreduce custom output"
date: "2018-04-10"
description: "This blog will tell you how to perform custom output file for Mapreduce in Hadoop"
categories:
    - "Hadoop"
    - "Cloud computing"
    - "Mapreduce"
---

# Introduction

Mapreduce is a very strong computing framework and we could use it to transform data to HDFS. Acutally, we could use Mapreduce to write data to any databse.

## Basic input and output

The key thing in Mapreduce is `Mapper` and `Reducer`. We use the following code snippets to build a mapper. Here, `Mapper<Object, Text, Text, Text>` means input key and value class and ouput key and value class. Please notice that we could not use `string` and `int` here, since it is not fit the requirement in HDFS. `value` is the line of input. We could split it into key-value pair to context. The context is the output for `Mapper`.



```java
 public static class YourMapper extends Mapper<Object, Text, Text, Text> {

    private Text word = new Text();
    private Text val = new Text();

    public void map(Object key, Text value, Context context)
        throws IOException, InterruptedException {
            ......
            context.write(word, val);
        }
 }
```

In reducer, we could use the following code snippets to write a reducer. We first needs to implement the `Reducer` class. In `reduce` function, we could see all `values` in `Iterable<Text> values` for the same key. Therefore, we could use your own function to combine all values. 

```java
 public static class yourReducer
      extends Reducer<Text, Text, Text, IntWritable> {
    private IntWritable result = new IntWritable();

    public void reduce(Text key, Iterable<Text> values, Context context)
        throws IOException, InterruptedException {
      int sum = 0;
      ....
      result.set(sum);
      context.write(key, result);
    }
  }
```

## Customize your output

In the previous part, we could only write data to text file. You could find from [here](https://github.com/sujee/hbase-mapreduce) about how to write file to HBase with official API and implement `TableReducer` not `Reducer`.

So what if I want to write my own output path, like writing to Redis directly. I think the following code could help you. I reference this [blog](https://content.pivotal.io/blog/making-hadoop-mapreduce-work-with-a-redis-cluster)

First you need to implement you `Mapper` and `Reducer` as before. Then you need to write a customized output format.

The key class implemented `RecordWriter`. The `<Text, Text>` is the output of `Reducer`. Use `write` function to do operation on any database. 

```java
 public static class YouOutputFormat extends OutputFormat<Text, Text> {

    // write some configuration in this function and you could use it in the main function
    public static void configure(...) {
      ...
    }

    // This method returns an instance of a RecordWriter for the task.  Note how
    // we are pulling the variables set by the static methods during
    // configuration
    public RecordWriter<Text, Text> getRecordWriter(TaskAttemptContext job)
        throws IOException, InterruptedException {

      return new YouRecordWriter(Hosts);
    }


    // The output committer is used on the back-end to, well, commit output.
    // Discussion of this class is out of scope, but more info can be found here
    public OutputCommitter getOutputCommitter(TaskAttemptContext context)
        throws IOException, InterruptedException {
      // use a null output committer, since
      return (new NullOutputFormat<Text, Text>()).getOutputCommitter(context);
    }

    //The key class is here. The <Text, Text> is the output of Reducer
    public static class YourRecordWriter extends RecordWriter<Text, Text> {

      private Jedis j;

      public YourRecordWriter(...) {
            ....
      }

      public void write(Text key, Text value)
          throws IOException, InterruptedException {
      
        // Write the key/value pair
      
      }

      @Override
      public void close(TaskAttemptContext context)
          throws IOException, InterruptedException {
      // finish writing

      }
    }
  } 
```

I hope this blog will be helpful. Thank you for reading!