title: MapReduce编程模型简述
date: 2019-02-28 16:06:53
tags: [hadoop]
categories: [开发]
---
#### 什么是MapReduce
```
- 源自Google的MapReduce论文
- 发表于2004年12月
```

#### 特点
```
- 易于编程
- 如果要手工开发需要考虑的点
  -  输入数据要如何进行拆分
  -  数据的本地性怎么办
  -  作业如果挂了怎么进行容错
- 良好的拓展性，很容易线性拓展
- 高容错性
  -  HDFS的数据是多副本的，就近取副本的数据进行处理
  -  当前计算的任务如果挂了会自动转移到其他节点上去
- 适合离线数据的处理（大吞吐量）
```

<!--more-->

#### 不擅长的场景
```
- 实时计算
- MapReduce每次都会开启一个Map进程和一个Reduce进程
创建和销毁太耗费时间和资源
数据是存放在磁盘的，运算起来速度太缓慢


- 流式计算，不能计算动态数据，程序起来之后新增时不行的

- DAG计算
- 多个任务之间存在依赖关系
- mapreduce为什么不适合dag计算？
因为他的计算速度比较慢，计算出来的中间结果等等都会写到磁盘上，输出的结果也会到磁
盘上，读写磁盘就意味着性能不能达到实时的要求。做历史数据的批处理比较好。但spark
等实时框架计算过程是用内存的，中间结果也可以放在内存中，所以很快，也就能达到实时
的要求了
```

#### MapReduce编程模型
```
- input&splitting
- map&shuffling&reduce
- output
```

![](https://upload-images.jianshu.io/upload_images/2572206-fb772d0741372d92.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
```
- Input：输入数据
- Splitting：切分
- *Mapping（开发）*：将Splitting的数据进行拆分，单条数据统计
- Shuffling：按照key的规则，放置到一起
- *Reducding（开发）*：归并处理
- output：输出

- 易开发：仅仅在Mapping和Reducding我们进行开发
```


#### WordCount实例
```
import java.io.IOException;
import java.util.StringTokenizer;

import org.apache.hadoop.conf.Configuration;
import org.apache.hadoop.fs.Path;
import org.apache.hadoop.io.IntWritable;
import org.apache.hadoop.io.Text;
import org.apache.hadoop.mapreduce.Job;
import org.apache.hadoop.mapreduce.Mapper;
import org.apache.hadoop.mapreduce.Reducer;
import org.apache.hadoop.mapreduce.lib.input.FileInputFormat;
import org.apache.hadoop.mapreduce.lib.output.FileOutputFormat;

public class WordCount {

  // 实现mapper方法
  public static class TokenizerMapper
       extends Mapper<Object, Text, Text, IntWritable>{

    private final static IntWritable one = new IntWritable(1);
    private Text word = new Text();

    public void map(Object key, Text value, Context context
                    ) throws IOException, InterruptedException {
      // 根据table键或者空格进行分割
      StringTokenizer itr = new StringTokenizer(value.toString());
      // 循环迭代分割的对象
      while (itr.hasMoreTokens()) {
        // 将分割的字符set进word中，并给每一个数据置1
        word.set(itr.nextToken());
        context.write(word, one);
      }
    }
  }

  // 继承reduce方法
  public static class IntSumReducer
       extends Reducer<Text,IntWritable,Text,IntWritable> {
    private IntWritable result = new IntWritable();

    public void reduce(Text key, Iterable<IntWritable> values,
                       Context context
                       ) throws IOException, InterruptedException {
      int sum = 0;
      // 统计map中value出现的次数，写入结果
      for (IntWritable val : values) {
        sum += val.get();
      }
      result.set(sum);
      context.write(key, result);
    }
  }

  public static void main(String[] args) throws Exception {
    // 构建Configuration 
    Configuration conf = new Configuration();
    // 构建Job类，设置Mapper作为主类
    Job job = Job.getInstance(conf, "word count");
    job.setJarByClass(WordCount.class);
    job.setMapperClass(TokenizerMapper.class);
    // 设置CombinerClass和ReducerClass
    job.setCombinerClass(IntSumReducer.class);
    job.setReducerClass(IntSumReducer.class);
    // 设置输出的key的类型
    job.setOutputKeyClass(Text.class);
    // 设置输出的value的类型
    job.setOutputValueClass(IntWritable.class);
    // 设置输入源
    FileInputFormat.addInputPath(job, new Path(args[0]));
    // 设置输出源
    FileOutputFormat.setOutputPath(job, new Path(args[1]));
    // 执行
    System.exit(job.waitForCompletion(true) ? 0 : 1);
  }
}
```