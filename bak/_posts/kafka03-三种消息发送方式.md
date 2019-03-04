title: kafka03 三种消息发送方式
date: 2018-09-20 15:10:25
tags: [java,kafka]
categories: [开发]
---

##### 发送即忘记
发送即忘记：**不关注**消息是否成功到达，大部分情况下，消息会成功送达至broker。但是还是会存在消息丢失的情况。
- 创建一个Properties对象，用于存储连接kafka所需的配置信息

<!--more-->

```
        // 创建一个Properties对象，用于存储连接kafka所需的配置信息
        Properties myKafkaProps = new Properties();
        // 配置kafka集群地址
        myKafkaProps.put("bootstrap.servers","132.232.14.247:9092");
        // 向kafka集群发送消息，出了消息值本身，还包括key信息，key信息用于消息在partition之间均匀分布
        // 发送消息的key，类型为String,使用String类型的序列化器
        myKafkaProps.put("key.serializer","org.apache.kafka.common.serialization.StringSerializer");
        //发送消息的value，类型为String,使用String类型的序列化器
        myKafkaProps.put("value.serializer","org.apache.kafka.common.serialization.StringSerializer");
```
- 创建KafkaProducer对象，传入配置信息
```
        // 创建一个kafkaProdecer对象，传入上面创建的Properties对象
        KafkaProducer<String,String> producer = new KafkaProducer<String, String>(myKafkaProps);
```
- 创建ProducerRecord对象，通过`topic、key、value`设置消息
```
        /**
         * 使用ProdecerRecord<String,String>(String topic,String key,String value)构造函数创建消息
         * 构造函数接受三个参数：
         * topic--告诉kafkaProducer消息发送到哪个topic；
         * key--告诉kafkaProducer，所发送消息的key值，需要和配置文件中的类型一直
         * value--告诉kafkaProducer，消息所发送的value值，同上
         */
        ProducerRecord<String,String> record = new ProducerRecord<String, String>("mySecondTopic","messageKey1","hello kafka");
```
- 调用send方法发送消息（发送了十次，方便我们查看发送时间进行比较）
```
        long startTime = System.currentTimeMillis();

        for (int i=0;i<10;i++) {
            try {
                //发送前面创建的消息对象record到kafka集群
                //发送消息过程中可能发送错误，如无法连接到kafka集群，所以在这里使用捕获异常代码
                producer.send(record);
            } catch (Exception e) {
                e.printStackTrace();
            }
        }

        long endTime = System.currentTimeMillis();
        System.out.println(endTime-startTime);
```
- 输出
```
1307
```
##### 同步发送
- 配置信息、producer和record的创建一致
```
        Properties kafkaProps = new Properties();
        kafkaProps.put("bootstrap.servers", "132.232.14.247:9094");
        kafkaProps.put("key.serializer", "org.apache.kafka.common.serialization.StringSerializer");
        kafkaProps.put("value.serializer", "org.apache.kafka.common.serialization.StringSerializer");

        KafkaProducer<String,String> producer = new KafkaProducer<String, String>(kafkaProps);
        ProducerRecord<String,String> record = new ProducerRecord<String, String>("mySecondTopic","messageKey","hello kafka");
```
- 在send()方法中使用Future对象获取发送消息返回的信息
```
                Future<RecordMetadata> future = producer.send(record);
                //producer的send方法返回Future对象，我们使用Future对象的get方法来实现同步发送消息。
                //Future对象的get方法会产生阻塞，直到获取kafka集群的响应，响应结果分两种：
                //1、响应中有异常：此时get方法会抛出异常，我们可以捕获此异常进行相应的业务处理
                //2、响应中无异常：此时get方法会返回RecordMetadata对象，此对象包含了当前发送成功的消息在Topic中的offset、partition等信息
                RecordMetadata recordMetadata = future.get();
                long offset=recordMetadata.offset();
                int partition=recordMetadata.partition();
                System.out.println("the message  offset : "+offset+" ,partition:"+partition+"。");
```
- 输出
```
the message  offset : 54 ,partition:3。
the message  offset : 55 ,partition:3。
the message  offset : 56 ,partition:3。
the message  offset : 57 ,partition:3。
the message  offset : 58 ,partition:3。
the message  offset : 59 ,partition:3。
the message  offset : 60 ,partition:3。
the message  offset : 61 ,partition:3。
the message  offset : 62 ,partition:3。
the message  offset : 63 ,partition:3。
6247
```
##### 异步发送
- 配置信息、producer和record的创建一致
```
        Properties kafkaProps = new Properties();
        kafkaProps.put("bootstrap.servers", "132.232.14.247:9094");
        kafkaProps.put("key.serializer", "org.apache.kafka.common.serialization.StringSerializer");
        kafkaProps.put("value.serializer", "org.apache.kafka.common.serialization.StringSerializer");

        KafkaProducer<String,String> producer = new KafkaProducer<String, String>(kafkaProps);
        ProducerRecord<String,String> record = new ProducerRecord<String, String>("mySecondTopic","messageKey","hello kafka");
```
- 发送消息时，传入一个实现了Callback接口的对象，此时发送消息不会阻塞，发送完成后，会调用Callback接口的onCompletion方法。
```
        long startTime = System.currentTimeMillis();

        for (int i=0;i<10;i++) {
            try {
                //发送前面创建的消息对象record到kafka集群
                //发送消息过程中可能发送错误，如无法连接到kafka集群，所以在这里使用捕获异常代码
                producer.send(record,new DemoProducerCallback());
            } catch (Exception e) {
                e.printStackTrace();
            }
        }

        long endTime = System.currentTimeMillis();
        System.out.println(endTime-startTime);
```

- DemoProducerCallback

```
import org.apache.kafka.clients.producer.Callback;
import org.apache.kafka.clients.producer.RecordMetadata;

public class DemoProducerCallback implements Callback {
    public void onCompletion(RecordMetadata recordMetadata,Exception exception){
        if (exception != null){
            exception.printStackTrace();
        }
        else {
            long offset = recordMetadata.offset();
            int partition = recordMetadata.partition();
            String topic = recordMetadata.topic();
            System.out.println("the message  offset : "+offset+" ,partition:"+partition+"。");
        }
    }
}
```

- 输出

```
981
the message  offset : 30 ,partition:0。
the message  offset : 31 ,partition:0。
the message  offset : 32 ,partition:0。
the message  offset : 33 ,partition:0。
the message  offset : 34 ,partition:0。
the message  offset : 35 ,partition:0。
the message  offset : 36 ,partition:0。
the message  offset : 37 ,partition:0。
the message  offset : 38 ,partition:0。
the message  offset : 39 ,partition:0。
```

##### *多线程异步发送方式
- 看注释

```
package com.shiyanlou.producer;

import org.apache.kafka.clients.producer.KafkaProducer;
import org.apache.kafka.clients.producer.ProducerRecord;
import scala.util.parsing.combinator.testing.Str;

import java.util.Properties;

public class MultiThreadProducer extends Thread {
    // 声明KafkaProducer类型的全局变量，由于在多线程环境，所以声明为final类型
    private final KafkaProducer<String,String> producer;
    /* 声明用于存储topic名称的全局变量，由于在多线程环境下，声明为final类型 */
    private final String topic;
    //设置一次发送消息的条数
    private final int messageNumToSend = 2;

    /**
     * 在构造函数中创建KafkaProducer对象
     * @param topicName topic名称
     */
    public MultiThreadProducer(String topicName){
        // 配置信息
        Properties kafkaProps = new Properties();
        kafkaProps.put("bootstrap.servers", "132.232.14.247:9094");
        kafkaProps.put("key.serializer", "org.apache.kafka.common.serialization.StringSerializer");
        kafkaProps.put("value.serializer", "org.apache.kafka.common.serialization.StringSerializer");
        producer = new KafkaProducer<String, String>(kafkaProps);
        //设置全局变量topic的值
        topic = topicName;
    }
    /**
     * 生产者线程执行函数，循环发送消息
     */
    @Override
    public void run() {
        //用于记录发送消息的条数，同时作为消息的key值
        int messageNo = 0;
        while (messageNo < messageNumToSend){
            //所发送消息的内容
            String messageContent = "Message_"+messageNo;
            //构造消息记录
            ProducerRecord<String,String> record = new ProducerRecord<String, String>(topic,messageNo+"",messageContent);
            //异步发送
            producer.send(record,new DemoProducerCallback());
            //累加
            messageNo++;
        }
        //刷新缓存，将消息发送到kafka集群
        producer.flush();
    }

    public static void main(String[] args) {

        long startTime = System.currentTimeMillis();

        for (int i=0;i<5;i++){
            new MultiThreadProducer("mySecondTopic").start();
        }

        long endTime = System.currentTimeMillis();
        System.out.println(endTime-startTime);

    }
}
```

- 输出

```
1625
the message  offset : 79 ,partition:3。
the message  offset : 46 ,partition:0。
the message  offset : 80 ,partition:3。
the message  offset : 47 ,partition:0。
the message  offset : 81 ,partition:3。
the message  offset : 48 ,partition:0。
the message  offset : 82 ,partition:3。
the message  offset : 49 ,partition:0。
the message  offset : 83 ,partition:3。
the message  offset : 50 ,partition:0。
```
