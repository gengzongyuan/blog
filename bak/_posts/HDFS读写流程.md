title: HDFS读写流程及副本放置机制
date: 2019-02-22T05:31:52.819Z
tags: [hadoop]
categories: [开发]
---
#### 写流程
![](https://upload-images.jianshu.io/upload_images/2572206-7ef8c4814106b564.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

<!--more-->

```
client端执行hadoop fs -put jdk.tar.gz /jdk.tar.gz          2block，3副本

1、  向NN发送写数据请求，NN判断*NN是否启动，jdk.tar.gz文件是否已经存在*
2、  OK，可以写
3、  请求写BLK1
4、  告知BLK1写入哪几个DN：DN1、DN2、DN3
5、  找到DN1，建立数据传输的连接
6、  DN1和DN2建立连接
7、  DN2和DN3建立连接
8、  步骤7成功
9、  步骤6成功
10、 步骤5成功
11、 传输BLK1写到DN1.... DN1传输数据到DN2.....DN2传输数据到DN3
12、BLK2的传输过程和BLK1的过程一样，重复3-11
13、待所有数据写完之后，将数据同步给NN，记录下mate信息
NN里存储下：
元数据信息：
jdk.tar.gz
BLK1: 3 DN1 DN2 DN3
BLK2: 3 DN2 DN3 DN4
```

#### 读流程
![](https://upload-images.jianshu.io/upload_images/2572206-c2173a6eebe7f157.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


```
1、  读数据请求
2、  OK，同时返回元数据信息
元数据信息：
jdk.tar.gz
BLK1: 3 DN1 DN2 DN3
BLK2: 3 DN2 DN3 DN4

3、  发送读取BLK1的请求
4、 数据流返回
5、 发送读取BLK2的请求
6、 数据流返回
```



#### 多副本放置顺序
- 副本顺序
```
第一个副本：放置在上传的DN节点上
如果是集群外提交，则随机挑选一台磁盘不太慢，CPU不太忙的节点上

DN节点提交-->放置在上传的DN节点上
集群外提交-->由DN判断随机挑选一台磁盘不太慢，CPU不太忙的节点上

第二个副本：
放置在第一个副本的不同的机架的节点上

第三个副本：
与第二个副本相同的不同节点上

假如还有更多的副本：随机放

生产上尽量在DN节点上进行读写动作，可以减少网络带宽
```

- 机架放置机器的计算
```
机架支持的总电源/每台机器占用的电源=机器
```