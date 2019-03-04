title: centos flume
date: 2018-09-20 16:24:10
tags: [linux,flume]
categories: [运维]
---


- flume下载地址
```
http://flume.apache.org/download.html
```
- 下载Flume文件包

<!--more-->

```
#进入/usr/local/flume/名录
cd /usr/local/flume/
#将 Flume 软件包下载到当前目录
wget http://apache.01link.hk/flume/1.7.0/apache-flume-1.7.0-bin.tar.gz
# 解压
tar -zxf apache-flume-1.7.0-bin.tar.gz
#重命名解压后的Flume目录。
sudo mv apache-flume-1.7.0-bin flume  
```
- 配置文件备份
```
# 进入配置目录
cd flume/conf/
#拷贝配置文件模板为 flume-conf.properties ，作为 Flume 的启动配置文件。
sudo cp flume-conf.properties.template flume-conf.properties 
```
