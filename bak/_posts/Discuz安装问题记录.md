title: Discuz安装问题记录
date: 2018-09-20 16:52:36
tags: [discuz]
categories: [运维]
---


##### 问题1
```
数据库连接错误
No such file or directory
```

<!--more-->


##### 解决方式
确认数据库用户名和密码正确（笔者懒得查，直接改了）
```
mysql -u root
use mysql;
update user set host = '123456' where user = 'root';
select host, user from user;
```
确认php和mysql的sock配置是否正确
```
mysql -u root
status
```
记录UNIX socket的值/phpstudy/data/mysql.sock
```
UNIX socket:		/phpstudy/data/mysql.sock
```
新建一个phpinfo.php文件，扔到www目录下，浏览器访问之
```
<?php   
phpinfo();   
?>   
```
![phpinfo](https://upload-images.jianshu.io/upload_images/2572206-501571a26d2f97c1.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
记录下
```
Configuration File (php.ini) Path:/phpstudy/server/php/etc
```
到/phpstudy/server/php/etc下查看php.ini文件
将以下参数值填为我们status时候保存的值
```
mysql.default_socket = /phpstudy/data/mysql.sock
mysqli.default_socket = /phpstudy/data/mysql.sock
pdo_mysql.default_socket = /phpstudy/data/mysql.sock
```


##### 问题2
```
ERROR 1044 (42000): Access denied for user ''@'localhost' to database 'mysql'
```
##### 解决方式
确认是否打开允许远程连接，访问/etc/my.cnf文件
```
[mysqld]
···
skip-grant-tables
···
```
添加skip-grant-tables配置











