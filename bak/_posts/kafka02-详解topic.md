title: kafka02 详解topic
date: 2018-09-20 15:10:23
tags: [java,kafka]
categories: [开发]
---

#### 详解topic

##### 1.参数说明
- 我们创建了两个 topic：myFirstTopic、mySecondTopic。创建 topic 的 shell 命令中用到了 partitions 和 replication-factor 等参数，在这里对 topic 的作用和参数含义再次说明：

<!--more-->


```
- topic --用于划分 Kafka 集群中的消息（Message）的逻辑概念，生产者发送消息，要发送给某个 topic；消费者消费消息，要消费某个（某几个）topic 中的消息。Kafka 集群中的消息都存储在 topic 中。
- Partitions --topic 中的消息是以 partition 分区的形式存储的，注意：- partition 是 Kafka 支持扩展和高并发处理的基础。每个 topic 包括1个或多个 partition。
- Offset --每个 partition 中的消息都是顺序存储的，每个消息都是有编号的，编号是顺序增长的。这个编号叫 offset，offset 记录了消息在 partition 中的位置。注意：offset 在一个 partition 内是有序的，多个 partition 之间的 offset 是独立存在的。
- Replication --Kafka 支持以 partition 为单位对消息进行冗余备份，每个 Partition 都必须配置至少1个 replication。replication-factor 参数指定了 replication 的个数，即 partition 的副本个数。
- Leader replica --在 partition 的多个副本 replication 中，有一个副本叫主副本（即Leader replica），所有消息消费者和消息生产者的读写请求都由 Leader replica 处理，这么做是为了保证一致性。其他副本叫从副本（即 Follower replica）,从副本 从 主副本处把数据更新同步到本地。
- ISR（全拼为：In-Sync Replica） --从副本中，如果从主副本处把数据更新同步到本地了，那么这个从副本处于ISR状态（已同步状态），如果没有完全与主副本同步，那么会被从ISR中踢出去，处于非同步状态。
```

##### 2.topic操作
- 新建


```
./kafka-topics.sh --zookeeper localhost:2181 --partitions 3 --create --replication-factor 3 --topic dashuTopic
```

- 修改，partition的数量只能增加不能减少


```
#注意：shell命令中的关键参数--alter,将partition数量由原来的3改为4.
./kafka-topics.sh --zookeeper localhost:2181 --alter --topic mySecondTopic --partitions 4    
```


- 查找

```
./kafka-topics.sh --zookeeper localhost:2181 --list
```

- 删除

```
#注意：shell命令中的关键参数--delete。
./kafka-topics.sh --zookeeper localhost:2181 --delete --topic myDeleteTopic  
Topic dashuTopic is marked for deletion.
Note: This will have no impact if delete.topic.enable is not set to true.

运行结果说明： myDeleteTopic 只是标记为删除，没有真正删除。
要想彻底删除，需在 server.properties 中设置 delete.topic.enable=true。运行如下命令验证这一点：
./kafka-topics.sh --zookeeper localhost:2181 --list
```

##### 3.使用java api操作java
- 创建一个maven项目，pom配置

```
    <dependencies>
        <dependency>
            <groupId>org.apache.kafka</groupId>
            <artifactId>kafka_2.10</artifactId>
            <version>0.10.2.0</version>
        </dependency>
    </dependencies>
```

- Download source
![](https://upload-images.jianshu.io/upload_images/2572206-3920305268698919.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/280)

- demo

```
import java.util.Properties;
import scala.collection.*;
import scala.collection.Iterable;
import kafka.admin.AdminUtils;
import kafka.utils.ZkUtils;

public class kafkaTopicTouch {
    public static void main(String[] args) {
        // TODO Auto-generated method stub
        //使用工具类连接zookeeper localhost:2181 session失效时间和连接超时时间都是30s,不启用kafka安全模式(false)
        ZkUtils zu = ZkUtils.apply("132.232.14.247:2181", 30000, 30000, false);
        //获取所有的topic配置信息，返回结果map中的key是topic名称
        Map<String, Properties> map = AdminUtils.fetchAllTopicConfigs(zu);
        //获取所有的topic名称
        Iterable<String> allTopics = map.keys();
        //将topic名称用&拼接起来
        String topicResult = allTopics.mkString("&");
        //将拼接结果打印到控制台上
        System.out.println(topicResult);
    }
}
```


