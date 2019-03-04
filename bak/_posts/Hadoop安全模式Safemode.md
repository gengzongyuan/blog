title: Hadoop安全模式Safemode
date: 2019-02-22 19:57:49
tags: [hadoop]
categories: [开发]
---
- 官网阅读
```
During start up the NameNode loads the file system state from the fsimage and the edits log 
file. It then waits for DataNodes to report their blocks so that it does not prematurely start 
replicating the blocks though enough replicas already exist in the cluster. During this time 
NameNode stays in Safemode. Safemode for the NameNode is essentially a read-only 
mode for the HDFS cluster, where it does not allow any modifications to file system or 
blocks. Normally the NameNode leaves Safemode automatically after the DataNodes have 
reported that most file system blocks are available. If required, HDFS could be placed in 
Safemode explicitly using bin/hdfs dfsadmin -safemode command. NameNode front page 
shows whether Safemode is on or off. A more detailed description and configuration is 
maintained as JavaDoc for setSafeMode().


- 在启动时候，NN会进入Safemode状态
- Safemode时**无法进行数据的读写操作**
- 当NN收到DN的report后，并且报告中的数据达到一个阈值的时候，才会解除Safemode
```

<!--more-->

- 相关配置
```
hdfs-site.xml

- dfs.namenode.replication.min
最少的---文件block副本数量，默认：1
- dfs.namenode.safemode.threshold-pct
最小的---副本数达到要求的block的数量占总block的百分比，默认：0.9999f
- dfs.namenode.safemode.min.datanodes
最小的---当前可用的DN节点数量，默认：0
- dfs.namenode.safemode.extension
最小的---满足前面三个条件可以维持的时间，单位毫秒，默认：1
```
- 安全模式操作
```
bin/hdfs dfsadmin -safemode get/enter/leave/wait

- get：查看当前状态
- enter：进入安全模式
- leave：强制离开安全模式
- wait：一直等待直到安全模式结束
```
- 50070状态查看
![](https://upload-images.jianshu.io/upload_images/2572206-8b2510ab565ce425.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


参考文章
> [HDFS安全模式详解](https://blog.csdn.net/bingduanlbd/article/details/51900512)