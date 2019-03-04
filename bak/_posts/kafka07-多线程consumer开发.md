title: kafka07 多线程consumer开发
date: 2018-09-20 15:10:34
tags: [java,kafka]
categories: [开发]
---

#### 多线程consumer开发
之前写的comsumer是非线程安全的，在多线程环境中，需要使用不同的kafkaConsumer对象
- MyMultiThreadComsumer 


<!--more-->

```
package com.shiyanlou.consumer;

import org.apache.kafka.clients.consumer.ConsumerRecord;
import org.apache.kafka.clients.consumer.ConsumerRecords;
import org.apache.kafka.clients.consumer.KafkaConsumer;
import org.apache.kafka.common.serialization.StringDeserializer;
import scala.util.parsing.combinator.testing.Str;

import javax.sound.midi.Track;
import java.util.List;
import java.util.Properties;

//实现Runnable接口，用于多线程环境
public class MyMultiThreadComsumer implements Runnable {
    //声明KafkaConsumer对象，用于订阅topic，读取消息
    private final KafkaConsumer<String,String> consumer;
    // 声明topic列表变量，作为KafkaConsumer订阅的topic列表
    private final List<String> topics;
    // 声明一个id变量，用于区分不同的consumer对象，方便查看哪个consumer消费了哪些数据
    private final int id;
    //构造函数，实例化上面声明的三个变量
    public MyMultiThreadComsumer(int id,String groupId,List<String> topics){
        this.id = id;
        this.topics =  topics;
        Properties props = new Properties();
        props.put("bootstrap.servers","132.232.14.247:9092");
        props.put("group.id",groupId);
        props.put("key.deserializer", StringDeserializer.class.getName());
        props.put("value.deserializer",StringDeserializer.class.getName());
        // 此处new了一个kafkaConsumer，保证一个KafkaConsumer对象用于一个线程中
        this.consumer = new KafkaConsumer<String, String>(props);
    }

    public void run() {
        try {
            //订阅topic
            consumer.subscribe(topics);
            while (true){
                //无限循环读取消息
                ConsumerRecords<String, String> records = consumer.poll(2000);
                for (ConsumerRecord<String,String> record:records){
                    //打印消息内容
                    System.out.println("Consumer-"+id+"s consumption message:partition="+record.partition()+",offset="+record.offset()+",key="+record.key()+",value="+record.value());
                }
            }
        }catch (Exception e){
            e.printStackTrace();
        }
    }
}
```

- MyMultiThreadConsumerTest

```
package com.shiyanlou.consumer;

import scala.util.parsing.combinator.testing.Str;

import java.util.Arrays;
import java.util.List;
import java.util.concurrent.Executor;
import java.util.concurrent.ExecutorService;
import java.util.concurrent.Executors;

public class MyMultiThreadConsumerTest {
    public static void main(String[] args) {
        //numConsumers变量值就是创建Kafka对象的数量
        int numConsumers = 3;
        // 声明消费者组名称，三个kafkaConsumer对象属于一个消费者组
        String groupId = "group2";
        //创建topic列表
        List<String> topics = Arrays.asList("mySecondTopic");
        //创建线程池对象，线程池中线程的数量为numConsumers'
        ExecutorService executor = Executors.newFixedThreadPool(numConsumers);
        for(int i=0;i<numConsumers;i++){
            MyMultiThreadComsumer consumer = new MyMultiThreadComsumer(i,groupId,topics);
            executor.submit(consumer);
        }
    }
}
```
