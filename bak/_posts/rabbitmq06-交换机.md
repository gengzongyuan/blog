title: rabbitmq06 交换机
date: 2018-09-20 14:57:47
tags: [rabbitmq,python]
categories: [开发]
---

#### 交换机
- direct（直连交换机）
- topic（主题交换机）
- headers（头部交换机）
- fanout（扇形交换机）

<!--more-->

##### 扇形交换机
- 声明一个扇形交换机`type='fanout'`

```
 channel.exchange_declare(exchange='logs',
                         type='fanout')
```

- 查看声明是否成功

```
[root@tencent1 sbin]# ./rabbitmqctl list_exchanges
Listing exchanges ...
fanout_exchange	fanout
```

- 发送消息时指定对应的exchange

```
channel.basic_publish(exchange='fanout_exchange',
                      routing_key="",
                      body="message")
```

- 声明两个队列并使用channel.queue_bind进行绑定

```
channel.queue_declare("queue1")
channel.queue_declare("queue2")
channel.queue_bind(exchange="fanout_exchange",queue="queue1")
channel.queue_bind(exchange="fanout_exchange",queue='queue2')
```

- 查看绑定情况

```
./rabbitmqctl list_bindings
Listing bindings ...
fanout_exchange	exchange	queue1	queue	queue1	[]
fanout_exchange	exchange	queue2	queue	queue2	[]
...done.
```

- 仅仅往exchange中发送消息(需要注意的是，扇形交换机会忽略routing_key值)

```
channel.basic_publish(exchange='fanout_exchange',
                      routing_key="", 
                      body="message")
```

- 查看队列中的消息状态

```
[root@tencent1 sbin]# ./rabbitmqctl list_queues name messages_ready messages_unacknowledged 
Listing queues ...
queue1	1	0
queue2	1	0
...done.

```

##### direct直连交换机
- 声明direct交换机

```
channel.exchange_declare(exchange="direct_exchange",exchange_type="direct")
```

- 声明三个队列，并设置不同的两个routing_key

```
channel.queue_declare(queue="direct_queue1")
channel.queue_declare(queue="direct_queue2")
channel.queue_declare(queue="direct_queue3")
channel.queue_bind(exchange="direct_exchange",queue="direct_queue1",routing_key="red")
channel.queue_bind(exchange="direct_exchange",queue="direct_queue2",routing_key="red")
channel.queue_bind(exchange="direct_exchange",queue="direct_queue2",routing_key="blue")
channel.queue_bind(exchange="direct_exchange",queue="direct_queue3",routing_key="blue")
```

- 发送消息

```
channel.basic_publish(exchange="direct_exchange",
                      routing_key="red",
                      body="red_message")

channel.basic_publish(exchange="direct_exchange",
                      routing_key="blue",
                      body="blue_message")
```

- 查看结果

```
[root@tencent1 sbin]# ./rabbitmqctl list_queues name messages_ready messages_unacknowledged 
Listing queues ...
direct_queue1	1	0
direct_queue2	2	0
direct_queue3	1	0
...done.
```

##### topic交换机
- topic交换机能够涵盖扇形交换机和直连交换机的功能。主要是通过灵活的定义routing_key进行实现
- 定义一个topic交换机

```
channel.exchange_declare(exchange="topic_exchange",exchange_type="topic")
```

- 声明三个队列，并灵活使用routing_key和交换机进行绑定

```
channel.queue_declare(queue="topic_queue1")
channel.queue_declare(queue="topic_queue2")
channel.queue_declare(queue="topic_queue3")

# 当routing_key=“一个任意单词.orange.一个任意单词”
channel.queue_bind(exchange="topic_exchange",queue="topic_queue1",routing_key="*.red.*")
# 当routing_key=“任意值”
channel.queue_bind(exchange="topic_exchange",queue="topic_queue2",routing_key="#")
# 当routing_key=“一个任意单词.orange.0个或1一个任意单词”
channel.queue_bind(exchange="topic_exchange",queue="topic_queue3",routing_key="*.red.#")
```

- 发送消息

```
# 三个队列都会接收到消息
channel.basic_publish(exchange="topic_exchange",
                      routing_key="n1.red.n3",
                      body="topic_message")

# 只有queue2和queue3能够接受到消息
channel.basic_publish(exchange="topic_exchange",
                      routing_key="n1.red",
                      body="topic_message")
# 只有queue2能够接受到消息
channel.basic_publish(exchange="topic_exchange",
                      routing_key="hjdasoij",
                      body="topic_message")
```







