title: kafka01 创建集群topic
date: 2018-09-20 15:10:20
tags: [java,kafka]
categories: [开发]
---

---
#### 创建集群topic
##### 1.创建topic

<!--more-->

- 创建topic

```
./kafka-topics.sh --create --zookeeper localhost:2181 --replication-factor 1 --partitions 1 --topic myFirstTopic
1、 kafka-topics.sh：Kafka 提供的一个 shell 脚本文件(位于 bin 目录中)，用于创建或查看 topic 信息。 
2、 --create：shell 脚本的参数，告诉 shell 脚本：要创建一个 topic。 
3、 --zookeeper localhost:2181：shell 脚本的参数，告诉 shell 脚本 Zookeeper 的地址，用于保存 topic 元数据信息。 
4、 --partitions 1：shell 脚本参数，告诉 shell 脚本：所创建的这个 topic 的 partition 个数为1 
5、 --replication-factor 1：shell脚本参数，告诉 shell 脚本：每个 partition 的副本数为1 
6、 --topic myFirstTopic：shell 脚本参数，告诉 shell 脚本：创建的 topic 的名称为 myFirstTopic。
```

- 查看topic是否创建成功

```
[root@tencent2 bin]# ./kafka-topics.sh --zookeeper localhost:2181 --list
myFirstTopic
```

- 启动producer向topic发送消息

```
./kafka-console-producer.sh --broker-list localhost:9092 --topic myFirstTopic
```

- 在新的终端启动消费者读取消息

```
./kafka-console-consumer.sh --bootstrap-server  localhost:9092 --topic myFirstTopic
```

- 在生产者终端输入一条消息，看看消费者终端是不是收到消息了

##### 2.详解kafka

```
- Kafka 的基本架构：一个 producer(生产者)发送消息到 Kafka 集群的某个 topic 中。consumer(消费者)可以消费某个(某几个) topic 中的消息。 Kafka 集群由多个 broker 组成。
- 什么是 broker？生产环境中，一台服务器上只会安装一个 Kafka 软件，这台服务器就是一个 Kafka Server，此时 Kafka Server 称为 broker；在我们的实验环境中，一个一台服务器上安装了三个 broker。
- 什么是 topic？发布到 Kafka 集群上的消息都属于某一个分类，这个分类称为 topic。topic 存储在 broker 中;
- 什么是 partition？一个 topic 包括多个 partition(分区)，partition 是物理上的概念。producer 发送的消息存储在 partition 中，每个 partition 可以有多个 replica(副本);
- 什么是 producer？producer 是用来产生消息的，负责将消息发送到 Kafka broker 的 topic 上;
- 什么是 consumer？consumer 是用来消费消息的，用于从 Kafka broker 上的 topic 中读取消息;
- broker 和 consumer 使用 Zookeeper 管理状态信息，如 offset 信息。
```

![](https://upload-images.jianshu.io/upload_images/2572206-b655bf212fedd885.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

```
上图中展示了 Kafka 相关的5个组件：producer、consumer、broker、topic、Zookeeper。
上图中展示了一个 broker，broker 中有一个名称为 KafkaTopic 的 Topic，KafkaTopic 中有3个 partition ，分别为 partition1 、partition2 、partition3 ；
消息生产者 producer 将消息发送到 topic 的 partition 中;
消息消费者 consumer 从 topic 的 partition 中读取消息；
consumer 和 broker 的元数据信息保存在 Zookeeper 中;
在 topic 中，每一个 partition 对应一个日志文件，partition 是一个有序的、不可变的消息序列；一个消息只能发送到一个 partition 上。
```

##### 3.建立第二个topic
- 创建topic，指定partitions:3,replication-fact:2

```
./kafka-topics.sh --create --zookeeper localhost:2181 --replication-factor 2 --partitions 3 --topic mySecondTopic
```

- 查看topic创建情况

```
./kafka-topics.sh --zookeeper localhost:2181 --list
```

- 查看topic属性

```
./kafka-topics.sh --zookeeper localhost:2181 --describe --topic mySecondTopic
Topic
Topic:mySecondTopic	PartitionCount:3	ReplicationFactor:2	Configs:
	Topic: mySecondTopic	Partition: 0	Leader: 2	Replicas: 2,1	Isr: 2,1
	Topic: mySecondTopic	Partition: 1	Leader: 0	Replicas: 0,2	Isr: 0,2
	Topic: mySecondTopic	Partition: 2	Leader: 1	Replicas: 1,0	Isr: 1,0

# 后面的 leader、replicas和ISR 涉及到 kafka 内核结构，我们后续单独开辟实验详细讲解，此时你只需要知道，这些参数保证了 kafka 集群的健壮性，保证消息不丢失，保证消息的高吞吐量。
```

##### 4.查看zookeeper上的元数据信息
- 进入zookeeper终端

```
./zookeeper-shell.sh localhost:2181
```

- 查看元数据信息

```
ls /  # 查看根节点信息
[cluster, controller_epoch, controller, brokers, zookeeper, admin, isr_change_notification, consumers, config]
ls /brokers  # 查看brokers 信息
[ids, topics, seqid]
ls /brokers/ids  # 节点
[0, 1, 2]
ls /brokers/topics  # topic
[mySecondTopic, myFirstTopic, myFirstTopicnihao, __consumer_offsets]
```


