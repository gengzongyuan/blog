title: Tomcat监控之probe3.0的安装与使用
date: 2018-09-20 12:56:40
tags: [监控,tomcat]
categories: [运维]
---

#### 提前准备
> 文件下载地址：https://pan.baidu.com/s/1kVqweAr

#### 安装
- 1. 将probe.war文件上传至tomcat/webapps文件目录下
- 2. 重启tomcat时war包会自动解压
- 3. 将messages_cn.properties文件上传至webapps/probe/WEB-INF下

<!--more-->

- 4. 在tomcat安装目录下修改conf/tomcat-users.xml文件，添加
``` shell
<?xml version='1.0' encoding='utf-8'?>
<tomcat-users>
  <role rolename="tomcat"/>
  <role rolename="role1"/>
  <role rolename="manager-gui"/> 
  <role rolename="manager-script"/>
  <role rolename="manager-jmx"/>
  <role rolename="manager-status"/>
  <user username="test1" password="1" roles="manager-gui"/>
  <user username="test2" password="2" roles="manager-script,manager-jmx,manager-status,poweruser,probeuser"/>
</tomcat-users>
```
- 5. 修改bin/catalina.bat文件，添加
```shell
set JAVA_OPTS=%JAVA_OPTS% -Dcom.sun.management.jmxremote
```
- 6. 重启tomcat
#### 使用
1. 访问http://localhost:8080/probe/
![image.png](http://upload-images.jianshu.io/upload_images/2572206-63d0e2e8983c7aee.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


