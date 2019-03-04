title: Hive安装部署
date: 2019-03-02T09:49:24.658Z
tags: [hive]
categories: [运维]
---
#### 前置环境准备
> hadoop:hdfs、yarn，参考`https://gengzongyuan.github.io/2019/02/15/Hadoop伪分布式部署/`
> jdk

- CDH版本软件下载
```
下载网站：`http://archive.cloudera.com/cdh5/cdh/5/`
下载地址：`wget http://archive.cloudera.com/cdh5/cdh/5/hive-1.1.0-cdh5.7.0.tar.gz`
```

<!--more-->

- 解压`tar -zxf hive-1.1.0-cdh5.7.0.tar.gz -C ~/app/`
- 配置环境变量
```
vim ~/.bash_profile

export HIVE_HOME=/home/hadoop/app/hive-1.1.0-cdh5.7.0
export PATH=$HIVE_HOME/bin:$PATH

source ~/.bash_profile
```
- 将mysql驱动上传到`/home/hadoop/app/hive-1.1.0-cdh5.7.0/lib`目录中
```
https://mvnrepository.com/artifact/mysql/mysql-connector-java/5.1.27
```

- 安装mysql,配置mysql（用于存放元数据信息）
```
- /home/hadoop/app/hive-1.1.0-cdh5.7.0/conf
- 建一个hive-site.xml文件
<configuration>
	<property>
  		<name>javax.jdo.option.ConnectionURL</name>
	    	<value>jdbc:mysql://11.11.11.11:3306/hiveDB?createDatabaseIfNotExist=true</v
    	</property>
    
	<property>
    		<name>javax.jdo.option.ConnectionDriverName</name>
        	<value>com.mysql.jdbc.Driver</value>
   	</property>

	<property>
  		<name>javax.jdo.option.ConnectionUserName</name>
    		<value>ro121212ot</value>
    	</property>

	<property>
  		<name>javax.jdo.option.ConnectionPassword</name>
    		<value>21212</value>
    	</property>
</configuration>
```
- hive启动
```
hive-1.1.0-cdh5.7.0/bin/hive

which: no hbase in (/root/app/hive-1.1.0-cdh5.7.0/bin:/root/app/hadoop-2.6.0-cdh5.7.0/bin:/root/app/hadoop-2.6.0-cdh5.7.0/sbin:/root/app/jdk1.8.0_171/bin:/usr/local/sbin:/usr/local/bin:/sbin:/bin:/usr/sbin:/usr/bin:/root/bin)

Logging initialized using configuration in file:/root/app/hive-1.1.0-cdh5.7.0/conf/hive-log4j.properties
WARNING: Hive CLI is deprecated and migration to Beeline is recommended.
hive> 

```
- 此时到我们的mysql库中看下，hive已经在我们的库了建 了许多表了
![](https://upload-images.jianshu.io/upload_images/2572206-3b69ea544e4c3c36.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


