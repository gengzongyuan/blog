title: kafka06 简单的consumer开发
date: 2018-09-20 15:10:31
tags: [java,kafka]
categories: [开发]
---

#### 简单的consumer开发

##### 简单的consumer

<!--more-->

- 配置信息
```
        Properties props = new Properties();
        //设置连接kafka集群的地址
        props.put("bootstrap.servers", "132.232.14.247:9092");
        props.put("group.id","group2");
        /**
         * 设置反序列化器，注意：consumer程序中key/value的反序列化器类型必须与producer程序中key/value的序列化器类型一致
         * 也就是说，如果producer程序中的key/value的序列化器类型为String(StringSerializer)类型
         * 那么consumer程序中
         */
        props.put("key.deserializer","org.apache.kafka.common.serialization.StringDeserializer");
        props.put("value.deserializer","org.apache.kafka.common.serialization.StringDeserializer");
```
- 创建KafkaConsumer对象
```
KafkaConsumer<String,byte[]> consumer = new KafkaConsumer<String, byte[]>(props);
```
- 订阅topic：mySecondTopic，即读取对应topic中的消息
```
consumer.subscribe(Arrays.asList("mySecondTopic"));
```
- 在循环中调用consumer的poll方法接收消息
```
        try {
            while (true){
                /**
                 * poll()方法会基于当前消费位置offset读取消息记录。
                 * 当group首次创建时，消费位置由reset策略决定（设置参数auto.offset.reset值：earliest或latest）
                 * 如果group中的consumer开始commit提交offset，那么当前消费位置以最后一次提交的offset为准
                 * poll(100)：100是获取消息的等待超时时间，单位毫秒，不能为负数
                 * 如果有消息记录可以读取，poll方法会理科返回。如果没有消息可以获取，poll方法会等待，直至超时
                 */
                ConsumerRecords<String,byte[]> records = consumer.poll(2000);
                for (ConsumerRecord<String,byte[]> record : records){
                    System.out.println("MyFirstConsumer's consumption message：partition="+record.partition()+",offset="+record.offset()+",key="+record.key()+",value="+record.value());
                }
            }
        }catch (Exception e){
            e.printStackTrace();
        }finally {
            /**
             * 关闭consumer，在用完consumer对象时，要关闭它。
             * 这么做不仅会清理在用socket连接
             * 而且会通知group：我离开了！这样group就可以将其消费的partition分配给其他consumer
             */
            consumer.close();
        }
```
##### consumer知识点

- consumer group消费者组
在props中，我们配置了一个group.id代表一个消费者组，消费者组具有以下特性

```
props.put("group.id","group2");
```

```
- 当topic中的partition为4时候，消费者组中有1个consumer，那么这个comsumer会消费所有的partition
- 当topic中的partition为4时候，消费者组中有2个consumer，那么每个comsumer会消费2个partition
- 当topic中的partition为4时候，消费者组中有5个consumer，那么四个comsumer会对应4个partition，有一个consumer是空闲的
***********
- 当topic中的partition为4时候，现在有两个消费者组，每个消费者组有4个comsumer。
- 那么两个消费者组是相互独立的，也就是说8个comsumer都在工作
```

- 查看mySecondTopic中的partition数量```4个```

```
[root@tencent2 bin]# ./kafka-topics.sh --describe --zookeeper localhost:2181 --topic mySecondTopic
[2018-09-11 18:05:15,638] INFO Accepted socket connection from /127.0.0.1:35734 (org.apache.zookeeper.server.NIOServerCnxnFactory)
[2018-09-11 18:05:15,650] INFO Client attempting to establish new session at /127.0.0.1:35734 (org.apache.zookeeper.server.ZooKeeperServer)
[2018-09-11 18:05:15,672] INFO Established session 0x165c7a392630003 with negotiated timeout 30000 for client /127.0.0.1:35734 (org.apache.zookeeper.server.ZooKeeperServer)
Topic:mySecondTopic	PartitionCount:4	ReplicationFactor:2	Configs:
	Topic: mySecondTopic	Partition: 0	Leader: 2	Replicas: 2,1	Isr: 1,2
	Topic: mySecondTopic	Partition: 1	Leader: 0	Replicas: 0,2	Isr: 0,2
	Topic: mySecondTopic	Partition: 2	Leader: 1	Replicas: 1,0	Isr: 0,1
	Topic: mySecondTopic	Partition: 3	Leader: 2	Replicas: 2,1	Isr: 1,2
```

- group.id = group1开启5个comsumer，并且发送消息，可以观察到有一个comsumer是没有进行工作的
- group.id = group1开启4个comsumer,group.id = group2开启4个comsumer.可以观察到8个comsumer都在工作

