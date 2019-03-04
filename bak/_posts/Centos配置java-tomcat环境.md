title: Centos配置java+tomcat环境
date: 2018-09-20 16:07:56
tags: [tomcat,java]
categories: [运维]
---


> 系统：
jdk：jdk-8u161-linux-x64.tar
tomcat：8.0.50
链接：https://pan.baidu.com/s/1HSbXjRJmwmj_nukHSVrDRQ 密码：6aew

<!--more-->

#### 安装jdk环境
- 1. 在网盘下载jdk文件，通过ftp工具上传到对应文件夹中，笔者放到了/home/gzyDesktop/java文件夹

```
tar -zxvf jdk-8u161-linux-x64.tar.gz  -C /usr/local #解压到/usr/local文件夹
vi /etc/profile
---------------------------
export JAVA_HOME=/usr/local/jdk1.8.0_161
export PATH=$JAVA_HOME/bin:$PATH
export CLASSPATH=.:$JAVA_HOME/lib/dt.jar:$JAVA_HOME/lib/tools.jar
----------------------------
source /etc/profile  # 刷新配置文件
```
- 2. 检查安装效果
```
[root@cui java]# java -version
java version "1.8.0_161"
Java(TM) SE Runtime Environment (build 1.8.0_161-b12)
Java HotSpot(TM) 64-Bit Server VM (build 25.161-b12, mixed mode)
```


#### 安装tomcat环境
- 1. 网盘下载tomcat文件apache-tomcat-8.0.50.tar，使用ftp工具上传到对应文件夹，笔者文件夹为/home/gzyDesktop/tomcat
```
tar -zxvf apache-tomcat-8.0.50.tar.gz  -C /usr/local    #解压到/usr/local文件夹
cd /usr/local/apache-tomcat-8.0.50/bin   #访问/usr/local/apache-tomcat-8.0.50/bin目录
./startup.sh  #启动tomcat
```
- 2. 检验安装效果
```
wegt ip:8080
[root@cui bin]# wget 47.94.108.11:8080
--2018-03-16 10:21:13--  http://47.94.108.11:8080/
Connecting to 47.94.108.11:8080... connected.
HTTP request sent, awaiting response... 200 OK
```

