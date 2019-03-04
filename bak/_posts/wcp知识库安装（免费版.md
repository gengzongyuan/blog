title: wcp知识库安装（免费版)
date: 2018-09-20 16:51:06
tags: [wcp知识库]
categories: [运维]
---


> 前言
WCP 是一套 BS 架构的知识管理系统、知识库系统，通过在服务器端进行部署后，可在客户端通过浏览器进行访问，亦可通过移动端浏览器访问。它能提供团队知识库建设的一整套功能，从知识创建、知识更新、知识推送、全文检索、在线office文档预览 到 知识评价等功能。 
相关文件下载地址：https://pan.baidu.com/s/1pNmip31 密码：ol89

<!--more-->


### 网站安装与使用

- １.　网站预览http://www.wcpdoc.com/home/Pubindex.html
![wcp知识库](http://upload-images.jianshu.io/upload_images/2572206-23969494023c7dfa.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

- 2. 下载相关文件
1）Apache_OpenOffice_4.1.3_Win_x86_install_zh-CN     
2）WCP3.2.5.free.win.Setup
![下载](http://upload-images.jianshu.io/upload_images/2572206-fd62906a6cf5220d.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


- 3. 安装先打开Apache_OpenOffice_4.1.3_Win_x86_install_zh-CN进行安装（WCP功能支持office拓展,所以需要安装此软件）

- 4. 安装WCP3.2.5.free.win.Setup

- 5. 执行C:\wcp3server\startupWCP.bat开启服务

- 6. 默认的WCP服务端口为8989，可以通过http://localhost:8989/wcp可以访问到服务（系统默认的管理员账号为sysadmin密码为111111）
![WCP首页](http://upload-images.jianshu.io/upload_images/2572206-725a599dbf6a75db.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

- 7. 端口修改：打开wcp3server\server\conf\server.xml，找到如下字段，对端口进行修改即可


```
    <Connector port="8989" protocol="HTTP/1.1"
               connectionTimeout="20000"
               redirectPort="8443" />
```




