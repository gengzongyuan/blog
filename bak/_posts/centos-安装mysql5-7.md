title: centos 安装mysql5.7
date: 2018-09-20 12:48:00
tags: [mysql]
categories: [运维]
---

**安装**
```
yum -y install https://repo.mysql.com/mysql57-community-release-el6-11.noarch.rpm
yum update -y
yum -y install mysql-community-server
service mysqld start
```
<!--more-->
**查看密码**
```
grep 'temporary password' /var/log/mysqld.log
# 显示如下
2017-12-20T15:32:24.436697Z 1 [Note] A temporary password is generated for root@localhost: th.otyKdt0f.
```
```
mysql -uroot -p
# 输入上面生成的密码登录
```
密码就是：```th.otyKdt0f.```

**修改密码**
```
ALTER USER 'root'@'localhost' IDENTIFIED BY 'MyNewPass4!';
```

**修改远程登录**
```
use mysql;
GRANT ALL PRIVILEGES ON *.* TO 'root'@'%' IDENTIFIED BY 'password' WITH GRANT OPTION;
FLUSH PRIVILEGES; 
quit();

service mysqld restart
service iptables stop
```
