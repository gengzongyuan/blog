title: rabbitmq05 公平调度
date: 2018-09-20 14:56:55
tags: [rabbitmq,python]
categories: [开发]
---

#### 公平调度
> 不知道你有没有发现，如果在启动worker前消息队列中已经存在很多个消息了，当启动worker的时候会一股脑的把所有的消息都拿过去。如果之前消息队列中有1W条消息，那这1W条消息要消费多久啊！！

##### 消息长度定义
- 使用basic_qos定义当前worker同一时间能够拿到手的消息的上限
```
channel.basic_qos(prefetch_count=1)
```
