title: centos memcache安装
date: 2018-09-20 12:46:54
tags: [memcache]
categories: [运维]
---

**Libevent**
```
yum -y install gcc
wget https://github.com/downloads/libevent/libevent/libevent-2.0.21-stable.tar.gz
tar -zxvf libevent-2.0.21-stable.tar.gz
cd libevent-2.0.21-stable
./configure -prefix=/usr/local/libevent && make && make test && sudo make install
```
<!--more-->
**memcached**
```
wget http://www.memcached.org/files/memcached-1.4.21.tar.gz
tar -zxvf memcached-1.4.21.tar.gz
cd memcached-1.4.21
./configure --prefix=/usr/local/memcached --with-libevent=/usr/local/libevent
make && make test && sudo make install
```
```
# 启动

/usr/local/bin/memcached -d -m 100 -u root -l 192.168.0.111 -p 12000 -c 512 -P /MEMPID/ memcached.pid

 -d选项是启动一个守护进程，  
  
-m是分配给Memcache使用的内存数量(缺省：64MB)，单位是MB，我这里是10MB，  
  
-u是运行Memcache的用户，我这里是root，  
  
-l监听服务器IP地址，如果有多个地址的话，这里指定了服务器IP地址192.168.70.178  
  
-p是设置Memcache监听的端口(缺省：11211)，我这里设置了12000，最好是1024以上的端口，  
  
-c选项是最大运行的并发连接数，默认是1024，此次512，按照服务器的负载量来设定，  
  
-P是设置保存Memcache的pid文件，我这里是保存在 / MEMPID/ memcached.pid  
```
```
#  测试

telnet 192.168.0.111 12000
```
