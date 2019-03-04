title: rabbitmq02 模拟工作请求
date: 2018-09-20 13:19:53
tags: [rabbitmq,python]
categories: [开发]
---

#### 模拟工作请求

##### 1.发送消息
<!--more-->
- 使用input建立一个可以指定发送内容的sender

```
import pika
import sys

connection = pika.BlockingConnection(pika.ConnectionParameters('XXXX'))
channel = connection.channel()

message = input("请输入发送内容:") or 'Hello World!'
channel.basic_publish(exchange='',
                      routing_key='hello',
                      body=message)

print('[X] Sent %r'%message)
```

##### 2.接收消息
- 每接收到1个点，我们就sleep1秒，来模拟真正的工作

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

channel.basic_consume(callback,
                      queue='hello',
                      no_ack=True)

channel.start_consuming()
```

##### 3.开始试验吧
- 开启两个worker
- 发送消息观察worker中的内容
- 可以看到rabbitmq会按照顺序把消息发送给每个消费者。默认采用一种轮询的方式


