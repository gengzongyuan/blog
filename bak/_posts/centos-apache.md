title: centos apache
date: 2018-09-20 16:21:53
tags: [apache,linux]
categories: [运维]
---

下载apr
```
wget http://mirrors.hust.edu.cn/apache//apr/apr-1.6.3.tar.gz
tar -zxvf apr-1.6.3.tar.gz
./configure --prefix=/usr/local/apr
make && make install
```

<!--more-->

安装apr-util
```
yum install expat-devel
wget http://archive.apache.org/dist/apr/apr-util-1.5.2.tar.gz
tar -zxvf apr-util-1.5.1.tar.gz 
./configure --prefix=/usr/local/apr-util -with-apr=/usr/local/apr/
make && make install
```
安装pcre
```
wget http://sourceforge.net/projects/pcre/files/pcre/8.32/pcre-8.32.tar.gz/download
tar -zxvf pcre-8.32.tar.gz
cd pcre-8.32
./configure  
make
make install
```

安装apache
```
wget http://apache.dataguru.cn//httpd/httpd-2.4.10.tar.gz
tar -zxvf httpd-2.4.10.tar.gz
cd httpd-2.4.10

./configure --prefix=/usr/local/apache --enable-mods-shared=all --with-apr=/usr/local/apr/ --with-apr-util=/usr/local/apr-util/  
make
make install
```
启动
```
/usr/local/apache/bin/apachectl start
```

