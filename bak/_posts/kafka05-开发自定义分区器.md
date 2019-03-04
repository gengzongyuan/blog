title: kafka05 开发自定义分区器
date: 2018-09-20 15:10:29
tags: [java,kafka]
categories: [开发]
---


#### 开发自定义分区器
上一节我们看到，如果在发送消息的时候没有指定对应的分区，会使用默认分区器对消息进行分区，这一节我们试着写一个自己的分区器

##### 默认分区器源码阅读

- 进入默认分区器

<!--more-->

```
org.apache.kafka.clients.producer.internals.DefaultPartitioner
```

```
**********
获取当前partition的数量
如果key==null：随机在partition中进行分配
如果key!=null：会对key进行hash取值，使用hash对partition数量进行取模
+ 所以key相同的消息，会发送到相同的分区
**********
    public int partition(String topic, Object key, byte[] keyBytes, Object value, byte[] valueBytes, Cluster cluster) {
        List<PartitionInfo> partitions = cluster.partitionsForTopic(topic);
        int numPartitions = partitions.size();
        if (keyBytes == null) {
            int nextValue = nextValue(topic);
            List<PartitionInfo> availablePartitions = cluster.availablePartitionsForTopic(topic);
            if (availablePartitions.size() > 0) {
                int part = Utils.toPositive(nextValue) % availablePartitions.size();
                return availablePartitions.get(part).partition();
            } else {
                // no partitions are available, give a non-available partition
                return Utils.toPositive(nextValue) % numPartitions;
            }
        } else {
            // hash the keyBytes to choose a partition
            return Utils.toPositive(Utils.murmur2(keyBytes)) % numPartitions;
        }
    }
```

##### 自定义分区器
- implements Partitioner
```
public class MyPartition implements Partitioner {
```
- 重写partition、close、configure方法
```
    public int partition(String topic, Object key, byte[] keyBytes, Object value, byte[] valueBytes, Cluster cluster) {
        System.out.println("Customed Partitioner is running...");
        List<PartitionInfo> partitions = cluster.partitionsForTopic(topic);
        int numPartitions = partitions.size();

        if (keyBytes == null){
            throw new InvalidRecordException("key cannot be null..");
        }else {
            if (((String)key).equals("1")){
                return 1;
            }else {
                return (Math.abs(Utils.murmur2(keyBytes)) % (numPartitions));
            }
        }
    }

    public void close() {

    }

    public void configure(Map<String, ?> configs) {

    }
```
- 使用
```
kafkaProps.put("partitioner.class","com.shiyanlou.MyPartition");
```
- demo

```
package com.shiyanlou;

import org.apache.kafka.clients.producer.Partitioner;
import org.apache.kafka.common.Cluster;
import org.apache.kafka.common.PartitionInfo;
import org.apache.kafka.common.record.InvalidRecordException;
import org.apache.kafka.common.utils.Utils;

import javax.rmi.CORBA.Util;
import java.util.List;
import java.util.Map;

public class MyPartition implements Partitioner {


    public int partition(String topic, Object key, byte[] keyBytes, Object value, byte[] valueBytes, Cluster cluster) {
        System.out.println("Customed Partitioner is running...");
        List<PartitionInfo> partitions = cluster.partitionsForTopic(topic);
        int numPartitions = partitions.size();

        if (keyBytes == null){
            throw new InvalidRecordException("key cannot be null..");
        }else {
            if (((String)key).equals("1")){
                return 1;
            }else {
                return (Math.abs(Utils.murmur2(keyBytes)) % (numPartitions));
            }
        }
    }

    public void close() {

    }

    public void configure(Map<String, ?> configs) {

    }
}
```

```
package com.shiyanlou.producer;

import org.apache.kafka.clients.producer.KafkaProducer;
import org.apache.kafka.clients.producer.ProducerRecord;
import org.apache.kafka.clients.producer.RecordMetadata;

import java.util.Properties;
import java.util.concurrent.Future;

public class MySecondProducer {
    public static void main(String[] args) {
        Properties kafkaProps = new Properties();
        kafkaProps.put("bootstrap.servers", "132.232.14.247:9094");
        kafkaProps.put("key.serializer", "org.apache.kafka.common.serialization.StringSerializer");
        kafkaProps.put("value.serializer", "org.apache.kafka.common.serialization.StringSerializer");
        kafkaProps.put("partitioner.class","com.shiyanlou.MyPartition");

        KafkaProducer<String,String> producer = new KafkaProducer<String, String>(kafkaProps);
        ProducerRecord<String,String> record = new ProducerRecord<String, String>("mySecondTopic","1","hello kafka");

        long startTime = System.currentTimeMillis();
        for (int i=0;i<10;i++) {
            try {
                //发送前面创建的消息对象ProducerRecord到kafka集群
                //发送消息过程中可能发送错误，如无法连接kafka集群，所以在这里使用捕获异常代码
                Future<RecordMetadata> future = producer.send(record);
                //producer的send方法返回Future对象，我们使用Future对象的get方法来实现同步发送消息。
                //Future对象的get方法会产生阻塞，直到获取kafka集群的响应，响应结果分两种：
                //1、响应中有异常：此时get方法会抛出异常，我们可以捕获此异常进行相应的业务处理
                //2、响应中无异常：此时get方法会返回RecordMetadata对象，此对象包含了当前发送成功的消息在Topic中的offset、partition等信息
                RecordMetadata recordMetadata = future.get();
                long offset=recordMetadata.offset();
                int partition=recordMetadata.partition();
                System.out.println("the message  offset : "+offset+" ,partition:"+partition+"。");
            } catch (Exception e) {
                e.printStackTrace();
            }
        }

        long endTime = System.currentTimeMillis();
        System.out.println(endTime-startTime);

        producer.close();
    }
}
```
