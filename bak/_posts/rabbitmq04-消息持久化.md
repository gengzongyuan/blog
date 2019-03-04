title: rabbitmq04 消息持久化
date: 2018-09-20 14:56:01
tags: [rabbitmq,python]
categories: [开发]
---

#### 消息持久化
> 之前我们考虑过，如果worker崩溃，消息将会丢失，所以引入了ack。那如果整个rabbitmq崩溃，那怎么办呢？

- 将队列声明为持久化

<!--more-->

```
# 在queue_declare的时候，使用durable=True定义队列
channel.queue_declare(queue='task_queue', durable=True)
```
- 将消息声明为持久化
```
# 在basic_publish的时候，引入propertie参数
channel.basic_publish(exchange='',
                      routing_key='task_queue',
                      body=message,
                      properties=pika.BasicProperties(
                         delivery_mode = 2, # make message persistent
                      ))
```
- 这样，我们的rabbitmq在异常情况下关闭或者重启的时候就能够对消息进行持久化的保存了。
