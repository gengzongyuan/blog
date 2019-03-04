title: kafka09 自定义提交offset
date: 2018-09-20 15:10:38
tags: [java,kafka]
categories: [开发]
---

#### 自定义提交offset
之前的实验中，采用的是自动提交offset的方式，这样下次进行pull操作的时候，就能根据我们之前提交的offset拿到最新的消息。
但是这种方式可能没办法满足一些特定的业务场景，下面让我们来手写提交offset的程序吧。
- 关闭自动提交offset

<!--more-->


```
// 关闭自动提交offset
props.put("enable.auto.commit", "false");
```
- 同步提交`consumer.commitSync();`
```
    public static void main(String[] args) {
        Properties props = new Properties();
        props.put("bootstrap.servers", "132.232.14.247:9092");
        props.put("key.deserializer", "org.apache.kafka.common.serialization.StringDeserializer");
        props.put("value.deserializer", "org.apache.kafka.common.serialization.StringDeserializer");
        props.put("group.id", "group1");
        // 关闭自动提交offset
        props.put("enable.auto.commit", "false");

        KafkaConsumer<String, String> consumer = new KafkaConsumer<String, String>(props);
        consumer.subscribe(Arrays.asList("mySecondTopic"));
        while (true){
            try {
                ConsumerRecords<String, String> records = consumer.poll(1000);
                for (ConsumerRecord<String,String> record:records){
                    System.out.println("s consumption message:partition="+record.partition()+",offset="+record.offset()+",key="+record.key()+",value="+record.value());
                }
                // 同步提交修改
                consumer.commitSync();
            }catch (Exception e){
                e.printStackTrace();
            }
        }
    }
```
- 异步提交`consumer.commitAsync(new OffsetCommitCallback() {}`
```
    public static void main(String[] args) {
        Properties props = new Properties();
        props.put("bootstrap.servers", "132.232.14.247:9092");
        props.put("key.deserializer", "org.apache.kafka.common.serialization.StringDeserializer");
        props.put("value.deserializer", "org.apache.kafka.common.serialization.StringDeserializer");
        props.put("group.id", "group1");
        // 关闭自动提交offset
        props.put("enable.auto.commit", "false");

        KafkaConsumer<String, String> consumer = new KafkaConsumer<String, String>(props);
        consumer.subscribe(Arrays.asList("mySecondTopic"));
        while (true){
            try {
                ConsumerRecords<String, String> records = consumer.poll(5000);
                for (ConsumerRecord<String,String> record:records){
                    System.out.println("s consumption message:partition="+record.partition()+",offset="+record.offset()+",key="+record.key()+",value="+record.value());
                }
                // 异步提交修改
                consumer.commitAsync(new OffsetCommitCallback() {
                    public void onComplete(Map<TopicPartition, OffsetAndMetadata> offsets, Exception exception) {
                        if (exception != null){
                            //处理异常逻辑
                        }else {
                            System.out.println("成功提交offset:"+offsets.toString());
                        }
                    }
                });
            }catch (Exception e){
                e.printStackTrace();
            }
        }
    }
```
###### 特定业务逻辑
- 按照partition同步进行offset的提交
```
try {
            while (true) {
                ConsumerRecords<String, String> records = consumer.poll(100);
                //遍历每个partition
                for (TopicPartition partition : records.partitions()) {
                    //获取指定partition中的消息记录
                    List<ConsumerRecord<String, String>> partitionRecords = records.records(partition);
                    //遍历指定partition中的消息记录
                    for (ConsumerRecord<String, String> record : partitionRecords){
                        //此处使用输出操作模拟项目中的业务处理。
                        System.out.println("MySyncConsumer2 consume message：partition="+record.partition()+",offset="+record.offset()+",key="+record.key()+",value="+record.value());
                    }
                    //获取partition中最后一条记录的offset
                    long lastoffset = partitionRecords.get(partitionRecords.size() - 1).offset(); 
                    //同步提交一个partition中的offset
                    consumer.commitSync(Collections.singletonMap(partition, new OffsetAndMetadata(lastoffset+ 1)));
                }
            }
        } finally {
            consumer.close();
        }
```
- 按照处理的消息量同步进行提交
```
try {
            while (true) {
                ConsumerRecords<String, String> records = consumer.poll(100);
                for (ConsumerRecord<String, String> record : records)
                {
                    //此处使用输出操作模拟项目中的业务处理。
                    System.out.println("MySyncConsumer3 consume message：partition="+record.partition()+",offset="+record.offset()+",key="+record.key()+",value="+record.value());
                    TopicPartition tp = new TopicPartition(record.topic(), record.partition());
                    OffsetAndMetadata om=new OffsetAndMetadata(record.offset(),"");
                    currentOffsets.put(tp,om);
                    //为方便测试，我们设置每5条记录提交一次
                    if (count % 5 == 0){
                        consumer.commitSync(currentOffsets); 
                    }
                    count++;
                }
                consumer.commitSync();
            }
        } finally {
            consumer.close();
        }
```
