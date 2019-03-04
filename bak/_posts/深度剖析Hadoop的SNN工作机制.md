title: 深度剖析Hadoop的SNN工作机制
date: 2019-02-20 19:39:13
tags: [hadoop]
categories: [开发]
---
- 元数据描述及持久化位置
```
元数据：HDFS的目录结构以及每个文件的BLOCK信息（id，副本系数、block存放在哪个DN上）
存放位置：${hadoop.tmp.dir}/name/....

NN持久化存储路径：/home/hadoop/app/tmp/dfs/name/current
SNN持久化存储路径：/home/hadoop/app/tmp/dfs/namesecondary/current
```

- NN数据和SNN数据对比
![](https://upload-images.jianshu.io/upload_images/2572206-bdbf0c979a997186.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


<!--more-->

- 流程图
![](https://upload-images.jianshu.io/upload_images/2572206-e88a3430c038867a.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
```
NN:
- 定期将内存中的元数据信息*序列化*到磁盘-->fsimage
- 记录时间段内所有对HDFS相关的操作指令-->edits日志

SNN:
s-1)SNN将DN的fsimage和edits日志复制到SNN上
s-2)反序列化数据到内存中
s-3)将edits中记录的所有对于HDFS做的操作更新到内存中去
s-5)生成新的fsimage文件
s-6)将新的fsimage文件复制到NN上
```
![](https://upload-images.jianshu.io/upload_images/2572206-e31b1479bf97bd38.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

```
1. NN会复制上一小时的edit文件和fsimage文件给到SNN
2. SNN将文件合并为新的fsimage文件
3. NN会check  SNN传递过来的fsimage文件,check完毕后放置在自己的目录下
4. 这一小时的日志会记录在NN的edits_inprogress日志中，待这一小时结束，变为editXXX-XXX文件

---------
每一小时重复这个循环

```