title: rabbitmq07 RPC服务
date: 2018-09-20 14:58:39
tags: [rabbitmq,python]
categories: [开发]
---

#### RPC服务
> RPC（Remote Procedure Call）远程服务调用，区别在于worker处理完成之后，会将处理的结果通过队列传递给client端。

<!--more-->

![服务过程](https://upload-images.jianshu.io/upload_images/2572206-26b2dcb6b6cbf150.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

##### 1.client端
- 新建一个连接类

```
import pika
import uuid

class FibonacciRpcClient(object):
```

- 在init中建立连接通道，并且回复replies声明独享的回调队列

```
    def __init__(self):
        self.connection = pika.BlockingConnection(pika.ConnectionParameters('123.207.154.169'))
        self.channel = self.connection.channel()
        result = self.channel.queue_declare(exclusive=True)# 断开连接时自动删除队列
        self.callback_queue = result.method.queue
        self.channel.basic_consume(self.on_response,# rensponse回调方法
                                   no_ack=True,
                                   queue=self.callback_queue)# 订阅这个消息队列，以便接收RPC的响应
```

- 书写一个on_response函数，来接收server服务器返回的内容

```
    # on_response回调函数对每一个响应执行一个非常简单的操作，检查每一个响应的corrrlation_id属性是否与我们期待的一致
    # 如果一致，将响应结果赋给response，然后跳出consuming循环
    def on_response(self,ch,method,props,body):
        if self.corr_id == props.correlation_id:
            self.response = body
```

- 书写call方法，用来发送消息，并通过properties的reply_to和correlation_id将回调队列和消息标记传给server

```
    # 接下来，我们定义我们的主要方法call方法，它执行真正的RPC请求
    # 在这个方法中，首先我们生成一个唯一的correlation_id，值并且保存起来，on_response回调函数会用它来回去符合要求的响应
    # 接下来，我们将带有reply_to和correlation_id属性的消息发布出去
    def call(self,n):
        self.response = None
        self.corr_id = str(uuid.uuid4())
        self.channel.basic_publish(exchange='',
                                   routing_key='rpc_queue',
                                   properties=pika.BasicProperties(
                                       reply_to=self.callback_queue,
                                       correlation_id = self.corr_id
                                   ),
                                   body=str(n))
        while self.response is None:
            self.connection.process_data_events()# 是一个等待消息的阻塞过程，连接的任何消息都可以使它脱离阻塞状态（有点像Ajax的事件等待机制）
        return int(self.response)
```

- 启动服务

```
fibonacci_rpc = FibonacciRpcClient()
print('[x] requesting fib(30) ')
response = fibonacci_rpc.call(100)
print('[.] Got %r'%(response,))
```

##### 2.server端

- 像往常一样，我们建立队列，声明队列

```
connection = pika.BlockingConnection(pika.ConnectionParameters('123.207.154.169'))
channel = connection.channel()
channel.queue_declare(queue='rpc_queue')
```

- 声明我们的fibonacci函数，它假设只有合法的正整数当做输入。

```
def fib(n):
    if n == 0:
        return 0
    elif n == 1:
        return 1
    else:
        return fib(n-1) + fib(n-2)
```

- 为basic_consume声明一个回调函数，这是RPC服务器端的核心。它执行实际的操作并且做出响应

```
def on_request(ch,method,props,body):
    n = int(body)
    print('[.] fib(%s)'%(n,))
    response = fib(n)

    ch.basic_publish(exchange='',
                          routing_key=props.reply_to,
                          properties=pika.BasicProperties(
                              correlation_id= \
                                  props.correlation_id
                          ),
                          body=str(response))
    ch.basic_ack(delivery_tag=method.delivery_tag)
```

- 或许我们希望能再服务器上多开几个线程。为了能将负载平均的分配到多个服务器，我们需要将prefetch_cou
nt设置好

```
channel.basic_qos(prefetch_count=1)
channel.basic_consume(on_request,queue='rpc_queue')
print('[x] Awaiting RPC requests')
channel.start_consuming()
```
