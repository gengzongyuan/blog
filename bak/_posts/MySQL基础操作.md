title: MySQL基础操作
date: 2019-02-13 17:24:05
tags: [mysql]
categories: [开发]
---
#### 命令行操作
- 登录
```
mysql
-u:用户名
-h:端口
-p:密码
-P:端口

mysql -uroot -pXXX -P 3306 -h 127.0.0.1
```
- 退出
```
exit
```

<!--more-->

#### 基础操作
- 查看databases
```
mysql> show databases;
+--------------------+
| Database           |
+--------------------+
| information_schema |
| mysql              |
| performance_schema |
| test               |
+--------------------+
4 rows in set (0.00 sec)
```
- 切换数据库
```
use mysql
```
- 查看数据库表
```
show tables;
```
- 修改密码
```
update user set password=password(123456) where user='root';
```
- 刷新权限
```
flush privileges;
```
