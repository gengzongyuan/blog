title: Hadoop常见配置
date: 2019-02-20 13:39:12
tags: [hadoop]
categories: [开发]
---
- 常见配置文件/home/hadoop/app/hadoop-2.6.0-cdh5.7.0/etc/hadoop
```
-rw-r--r-- 1 hadoop hadoop   884 Feb 13 22:34 core-site.xml  
-rw-r--r-- 1 hadoop hadoop  4294 Feb 13 22:30 hadoop-env.sh  JDK目录 hadoop家目录
-rw-r--r-- 1 hadoop hadoop   867 Feb 13 22:34 hdfs-site.xml
-rw-r--r-- 1 hadoop hadoop 11291 Mar 24  2016 log4j.properties
-rw-r--r-- 1 hadoop hadoop   758 Mar 24  2016 mapred-site.xml.template
-rw-r--r-- 1 hadoop hadoop    10 Mar 24  2016 slaves
-rw-r--r-- 1 hadoop hadoop   690 Mar 24  2016 yarn-site.xml
```

<!--more-->

- `core-site.xml`
```
- namenode配置
    <property>
        <name>fs.defaultFS</name>
        <value>hdfs://hadoop001:8020</value>
    </property>

- snn配置
<property>
	<name>dfs.namenode.secondary.http-address</name>
	<value>hadoop001:50090</value>
</property>
<property>
	<name>dfs.namenode.secondary.https-address</name>
	<value>hadoop001:50091</value>
</property>

```
- `slaves`
```
- datanode配置
hadoop001
```
- `hadoop-env.sh`
```
- jdk目录
export JAVA_HOME=/home/hadoop/app/jdk1.8.0_171

- pid文件目录
export HADOOP_PID_DIR=/home/hadoop/app/tmp
```
- `mapred-site.xml`
```
- 使用的调度器
    <property>
        <name>mapreduce.framework.name</name>
        <value>yarn</value>
    </property>

```
- `yarn-site.xml`
```
- yarn任务方式
    <property>
        <name>yarn.nodemanager.aux-services</name>
        <value>mapreduce_shuffle</value>
    </property>

```
...未完待续