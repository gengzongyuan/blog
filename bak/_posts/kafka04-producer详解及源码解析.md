title: kafka04 producer详解及源码解析
date: 2018-09-20 15:10:27
tags: [java,kafka]
categories: [开发]
---

#### producer详解及源码解析
##### 1、Producer消息发送的基本步骤
- (0) 创建 KafkaProducer 对象，此对象接收 Properties 类型的参数，我们配置了 bootstrap.servers、key.serializer、value.serializer 三个参数。
- (1) 接着创建了一个 ProducerRecord 对象，创建此对象时，我们传入了 topic、消息的 key 和消息的 value 等参数。然后我们使用(0)步骤中创建的 KafkaProducer 对象的 send 方法将消息发送了出去。上面两个步骤是我们开发的 Producer 程序所完成的工作。消息发送出去之后，接下来发生了什么呢？

<!--more-->

- (2) 消息发送出去之后,首先，KafkaProducer 对象会对消息的 key 和 value 进行序列化，序列化后的数据才可以通过网络传输。使用的序列化类就是我们配置的 key.serializer 和 value.serializer 两个参数的值所指向的类。
- (3) 接着，消息会发送到 partitioner，partitioner 负责将消息发送到 topic 的某个 partition。如果我们在创建 ProducerRecord 对象时声明了分区，那么 partitioner 会直接返回声明的分区。如果没有声明分区，partitioner 会选一个分区，通常会基于消息的 key 值做分区选择。一旦分区选定，producer 就知道了：消息要发送到哪个 topic 的哪个分区。
- (4) 接着，producer 会把发送到相同 topic，相同 partition 的消息进行打包，形成 Batch ,后续消息，如果有相同的 topic 和 partition，都会添加到相应的 Batch 中。producer 会启动一个独立的线程，将这些打包的消息批量发送到对应的 kafka broker。
- (5) 当 broker 收到消息，会发回一个响应。
- (6) 如果消息成功写入 Kafka，会响应一个 RecordMetadata 对象到 Java Producer 程序，其中包括 topic、partition 和 offset 等信息。如果消息没有成功写 入 Kafka，将会响应一个错误 error。
- (7) 当 producer 收到一个错误响应，producer 会尝试重发消息，当尝试次数达到配置的值时，仍未发送成功，此时会返回一个错误到 Java Producer 程序。

##### 2、Properties参数详解
- bootstrap.servers

```
props.put("bootstrap.servers", "132.232.14.247:9092");
***************
kafka的brokers列表， 列表中不要求包含所有的brokers，producer会根据连接上的broker查询到其他broker。
建议列表中至少包含两个brokers，因为即使一个broker连不上，可以使用另一个broker，提高程序的健壮性。
***************
```

- key.deserializer

```
props.put("key.deserializer","org.apache.kafka.common.serialization.StringDeserializer");
***************
- 即使只发送value也需要设置
- 需要配置全路径名的类
- 设置之后producer才会知道如何将这些java对象转换为二进制数组，用于网络传输
***************
```

- value.deserializer

```
props.put("value.deserializer","org.apache.kafka.common.serialization.StringDeserializer");
***************
同key
***************
```

- KafkaProducer

```
// 创建一个kafkaProdecer对象，传入上面创建的Properties对象
KafkaProducer<String,String> producer = new KafkaProducer<String, String>(myKafkaProps);
```

##### 3、源码解析
- ProducerRecord构造方法

```
# ProducerRecord提供了四个构造方法，我们可以指定
# topic、partition、timestamp、key、value
public ProducerRecord(String topic, Integer partition, Long timestamp, K key, V value)
public ProducerRecord(String topic, Integer partition, K key, V value)
public ProducerRecord(String topic, K key, V value)
public ProducerRecord(String topic, V value)
```

- send方法--一路进入dosend方法
  - 序列化key和value 
![](https://upload-images.jinshu.io/upload_images/2572206-bbde190cf45a4b93.png?imageMogr2/auuto-orient/strip%7CimageView2/2/w/1240)
- 构建partition并且添加到batch中```此时使用的分区器是默认的分区器```
![](https://upload-images.jianshu.io/upload_images/2572206-b0dee111186ec4d5.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
- 发送消息到kafka
![](https://upload-images.jianshu.io/upload_images/2572206-9ced7a5e067fdf05.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)














