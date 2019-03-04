title: kafka08 消费指定offset的消息
date: 2018-09-20 15:10:36
tags: [java,kafka]
categories: [开发]
---

#### 从头开始消费消息`seekToBeginning()`
- 配置信息

<!--more-->

```
Properties props = new Properties();
props.put("bootstrap.servers","132.232.14.247:9092");
props.put("key.deserializer","org.apache.kafka.common.serialization.StringDeserializer");
props.put("value.deserializer","org.apache.kafka.common.serialization.StringDeserializer");
props.put("group.id","group1");
```
- 新建一个KafkaConsumer
```
KafkaConsumer<String,byte[]> consumer = new KafkaConsumer<String, byte[]>(props);
```
- 通过`TopicPartition`指定要消费的partition
```
TopicPartition seekToEndPartition = new TopicPartition("mySecondTopic",3);
```
- 对consumer指定partition`assign`
```
consumer.assign(Arrays.asList(seekToEndPartition));
```
- 调用```consumer.seekToBeginning```指定从头开始消费
```
consumer.seekToBeginning(Arrays.asList(seekToEndPartition));
```
- 开始消费
```
ConsumerRecords<String, byte[]> records = consumer.poll(5000);
for(ConsumerRecord<String,byte[]> record:records){
System.out.println("s consumption message:partition="+record.partition()+",offset="+record.offset()+",key="+record.key()+",value="+record.value());
}
```
- demo 
```
public class MySeekToBeginningConsumer {
    public static void main(String[] args) {
        Properties props = new Properties();
        props.put("bootstrap.servers","132.232.14.247:9092");
        props.put("key.deserializer","org.apache.kafka.common.serialization.StringDeserializer");
        props.put("value.deserializer","org.apache.kafka.common.serialization.StringDeserializer");
        props.put("group.id","group1");

        KafkaConsumer<String,byte[]> consumer = new KafkaConsumer<String, byte[]>(props);

        try {
            TopicPartition seekToEndPartition = new TopicPartition("mySecondTopic",3);
            consumer.assign(Arrays.asList(seekToEndPartition));
            consumer.seekToBeginning(Arrays.asList(seekToEndPartition));
            ConsumerRecords<String, byte[]> records = consumer.poll(5000);
            for(ConsumerRecord<String,byte[]> record:records){
                System.out.println("s consumption message:partition="+record.partition()+",offset="+record.offset()+",key="+record.key()+",value="+record.value());
            }
        }catch (java.lang.Exception e){
            e.printStackTrace();
        }finally {
            consumer.close();
        }
    }
}
```
#### 从尾开始消费消息`seekToEnd()`
- 调用`consumer.seekToEnd`从末尾开始进行消费
```
···
consumer.seekToEnd(Arrays.asList(seekToEndPartition ));
···
```
- demo
```
public class MySeekToEndConsumer {

    public static void main(String[] args) {
        Properties props=new Properties();
        props.put("bootstrap.servers", "localhost:9092");
        props.put("group.id", "group1");
        props.put("key.deserializer", "org.apache.kafka.common.serialization.StringDeserializer");
        props.put("value.deserializer", "org.apache.kafka.common.serialization.StringDeserializer");

        KafkaConsumer<String,byte[]> consumer=new KafkaConsumer<String,byte[]>(props);
        try {
            TopicPartition seekToEndPartition = new TopicPartition("mySecondTopic", 1);
            consumer.assign(Arrays.asList(seekToEndPartition));
            consumer.seekToEnd(Arrays.asList(seekToEndPartition ));
            ConsumerRecords<String, byte[]> records=consumer.poll(1000);
            for(ConsumerRecord<String, byte[]> record : records){
                System.out.println("MySeekToEndConsumer consumer message:partition="+record.partition()+",offset="+record.offset()+",key="+record.key()+",value="+record.value());
            }
        } catch (Exception e) {
            e.printStackTrace();
        }finally{
            consumer.close();
        }
    }
}
```
#### 消费指定offset消息`seek()`
- consumer.seek
```
//指定seekPartition和offset
consumer.seek(seekPartition,10);
```

- demo


```

public class MySeekConsumer {
    public static void main(String[] args) {
        Properties props = new Properties();
        props.put("bootstrap.servers", "132.232.14.247:9092");
        props.put("group.id", "group1");
        props.put("key.deserializer", "org.apache.kafka.common.serialization.StringDeserializer");
        props.put("value.deserializer", "org.apache.kafka.common.serialization.StringDeserializer");

        KafkaConsumer<String, String> consumer = new KafkaConsumer<String, String>(props);

        try {
            TopicPartition seekPartition = new TopicPartition("mySecondTopic", 1);
            consumer.assign(Arrays.asList(seekPartition));
            consumer.seek(seekPartition,10);
            ConsumerRecords<String, String> records = consumer.poll(5000);
            for (ConsumerRecord record:records){
                System.out.println("s consumption message:partition="+record.partition()+",offset="+record.offset()+",key="+record.key()+",value="+record.value());
            }
        }catch (Exception e){
            e.printStackTrace();
        }finally {
            consumer.close();
        }
    }
}
```
