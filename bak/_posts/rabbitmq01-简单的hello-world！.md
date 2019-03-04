title: rabbitmq01 简单的hello world！
date: 2018-09-20 13:19:52
tags: [rabbitmq,python]
categories: [开发]
---


> 课前准备：https://gengzongyuan.github.io/2018/09/20/rabbitmq%E5%AE%89%E8%A3%85/  （单节点安装部分）

#### 简单的hello world！
##### 1.发送消息
- 安装pika包
```
pip install pika==0.9.5
```
<!--more--> 

- 创建一个连接（BlockingConnection、ConnectionParameters）
```
import pika
# 创建一个连接器
connection = pika.BlockingConnection(pika.ConnectionParameters('localhost'))
# 建立一个channel
channel = connection.channel()
```
- 使用queue_declare创建一个队列
```
channel.queue_declare(queue='hello')
```
- 使用basic_publish发送消息到队列
```
channel.basic_publish(exchange='', # 交换机
                      routing_key='hello',  # 队列
                      body='Hello World!')  # 消息
print " [x] Sent 'Hello World!'"
```

- 关闭连接
```
connection.close()
```
- 到服务器上查看队列是否都有了

```
./rabbitmqctl list_queues name messages_ready messages_unacknowledged 
```
##### 2.获取消息
- 保险起见先声明一个队列
```
channel.queue_declare(queue='hello')
```
- 使用basic_consume接收消息
```
def callback(ch, method, properties, body):
    print " [x] Received %r" % (body,)

channel.basic_consume(callback,
                      queue='hello',
                      no_ack=True) # 暂时先不用管
```





