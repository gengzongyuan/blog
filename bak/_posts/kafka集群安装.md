title: kafka集群安装
date: 2018-09-20 15:10:18
tags: [kafka,java]
categories: [运维]
---

- 下载安装

```
cd /usr/local
mkdir kafka
cd kafka
#下载 Kafka 软件包到当前目录中
wget http://labfile.oss.aliyuncs.com/courses/859/kafka_2.10-0.10.2.1.tgz  
tar -zxf kafka_2.10-0.10.2.1.tgz
mv kafka_2.10-0.10.2.1 kafka
# 后台启动zookepper
./zookeeper-server-start.sh ../config/zookeeper.properties &
jps查看是否启动成功
```

<!--more-->

- 修改配置文件

```
[root@tencent2 config]# vim server-1.properties 
broker.id=1
log.dir=/tmp/kafka-logs-1
listeners=PLAINTEXT://:9093
[root@tencent2 config]# vim server-2.properties
broker.id=2
log.dir=/tmp/kafka-logs-2
listeners=PLAINTEXT://:9094
```

- 修改kafka：JVM参数
笔者的机器配置比较渣，kafka默认启动配置1G的内存，如果本机内存不够会报错，可用内存>3.5G的同学可以跳过此步骤

```
vim kafka-server-start.sh 

if [ "x$KAFKA_HEAP_OPTS" = "x" ]; then
    export KAFKA_HEAP_OPTS="-Xmx512M -Xms512M"
fi
```

- 启动kafka集群

```
#切换到启动kafka命令所在目录
cd /usr/local/kafka/kafka/bin
#使用配置文件 server.properties 启动第一个 Kafka Broker，注意：命令最后的 & 符号表示以后台进程启动，启动完成后，按回车键，回到命令行，启动另一个 Kafka Broker。
./kafka-server-start.sh  ../config/server.properties & 
#使用配置文件 server-1.properties 启动第二个 Kafka Broker 。启动完成后，按回车键，回到命令行，启动另一个 Kafka Broker 。
./kafka-server-start.sh  ../config/server-1.properties & 
#使用配置文件 server-2.properties 启动第三个 Kafka Broker。启动完成后，按回车键，回到命令行。
./kafka-server-start.sh  ../config/server-2.properties &
#查看当前运行的java进程。如下图，出现三个 kafka 进程，说明三个 Broker 的 Kafka 集群启动成功。
jps
```
