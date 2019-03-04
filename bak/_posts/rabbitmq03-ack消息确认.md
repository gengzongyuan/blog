title: rabbitmq03 ack消息确认
date: 2018-09-20 13:22:19
tags: [rabbitmq,python]
categories: [开发]
---

#### ack消息确认
> ack的产生是因为，如果消息发送给一个worker了，如果再运行期间，worker挂掉了，那我们的消息也就跟着worker丢了，为了保证worker挂掉不会带着消息一起挂掉，产生了ack确认机制

<!--more-->
##### 模拟
- 发送一条很长的消息
- ./rabbitmqctl list_queues name messages_ready messages_unacknowledged查看消息是否到达队列
- 运行worker然后暂停
- ./rabbitmqctl list_queues name messages_ready messages_unacknowledged消息跟着worker一起丢失了

##### 处理消息
- 在回调函数中调用ch.basic_ack(delivery_tag = method.delivery_tag)来完成消息的消费确认

```
import pika
import time

connection = pika.BlockingConnection(pika.ConnectionParameters(
        host='localhost'))
channel = connection.channel()

channel.queue_declare(queue='hello')

print ' [*] Waiting for messages. To exit press CTRL+C'

def callback(ch, method, properties, body):
    print " [x] Received %r" % (body,)
    time.sleep(body.decode().count('.'))
    print " [x] Done"
    ch.basic_ack(delivery_tag = method.delivery_tag)

channel.basic_consume(callback,
                      queue='hello')
channel.start_consuming()
```

##### 校验结果
- 发送消息

```
[root@tencent1 sbin]# ./rabbitmqctl list_queues name messages_ready messages_unacknowledged 
Listing queues ...
hello	1	0
...done.
```

- 运行worker

```
[root@tencent1 sbin]# ./rabbitmqctl list_queues name messages_ready messages_unacknowledged 
Listing queues ...
hello	0	1
...done.
```

- 停止worker

```
[root@tencent1 sbin]# ./rabbitmqctl list_queues name messages_ready messages_unacknowledged 
Listing queues ...
hello	1	0
...done.
```
