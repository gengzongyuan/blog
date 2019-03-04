title: kafka10 kafka整合flume
date: 2018-09-20 15:10:40
tags: [java,kafka]
categories: [开发]
---

> flume安装参见https://www.jianshu.com/p/047c2a684771

#### kafka整合flume
##### 1.配置flume
- 添加

<!--more-->

```
vim flume-conf.properties
```

```
# 输入端，名称为r1
agent.sources = r1      
# 传输通道 ，名称为c1
agent.channels = c1     
# 输出端，名称为s1
agent.sinks = s1        

# 下面是source配置部分：        
# 设置输入端r1的类型为netcat，接收命令行输入
agent.sources.r1.type = netcat  
# 输入端绑定主机名/IP地址 localhost
agent.sources.r1.bind = localhost       
# 输入端绑定端口 9999
agent.sources.r1.port = 9999    
# 输入端对接传输通道c1
agent.sources.r1.channels = c1  


# 下面是channel配置部分：       
# 传输通道类型为内存模式 memory
agent.channels.c1.type = memory 
# 传输通道内存容量
agent.channels.c1.capacity = 100        


# 下面是sink配置部分：  
# 输出端s1的类型为kafkaSink（输出到kafka）
agent.sinks.s1.type = org.apache.flume.sink.kafka.KafkaSink     
# 输出到kafka topic：mySecondTopic（前面实验中此topic已创建）
agent.sinks.s1.topic = mySecondTopic    
# 输出端对应的broker列表：localhost:9092
agent.sinks.s1.brokerList = localhost:9092      
# 设置kafka响应次数
agent.sinks.s1.requiredAcks = 1 
# 批量发送消息大小，在这里设置为1，每次发送一条消息
agent.sinks.s1.batchSize = 1    
# 输出端对接传输通道c1
agent.sinks.s1.channel = c1     
```

- 打开kafka-console-consumer在控制台进行消息的消费

```
./kafka-console-consumer.sh -–bootstrap-server  localhost:9092 --topic mySecondTopic
```

- 进入flume的bin目录启动flume

```
./flume-ng agent -c conf -f ../conf/flume-conf.properties -n agent -Dflume.root.logger=INFO,console

agent：flume命令，启动一个flume agent。
-conf conf ：配置项，表明使用conf目录中的配置文件。
-f ../conf/flume-conf.properties：指定配置文件路径及名称
-n agent：等价于-name agent,指定agent的名称为agent。
-Dflume.root.logger=INFO,console ：指定日志级别（INFO）及输出位置（console）
```

- 打开一个新的窗口

```
yum install telnet
telnet
open localhost 9999
```

```
hello flume kafka
```

- 看一下console-consumer有没有收到消息吧

