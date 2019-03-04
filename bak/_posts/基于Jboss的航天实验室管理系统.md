title: 基于Jboss的航天实验室管理系统
date: 2018-09-20 16:51:59
tags: [jboss]
categories: [运维]
---

> 文件下载地址 https://pan.baidu.com/s/1hsTj1a8
> 环境：
> - jdk 1.8.0_20
> - windows 7

<!--more-->


### 前期准备
本航天实验管理系统是部署在Jboss上的，需要使用java环境，所以在部署前确认电脑已经安装jdk并配置相应的环境变量。

### 安装
1. 下载解压

![](http://upload-images.jianshu.io/upload_images/2572206-7b1d569349524ffe.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

2.访问bin目录，运行standalone.bat

![standalone.bat](http://upload-images.jianshu.io/upload_images/2572206-b6e6ba2f07e17998.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

3.系统提示Deployed "ROOTBASE.ear"  表示系统启动成功

![image.png](http://upload-images.jianshu.io/upload_images/2572206-675ee069853f7d01.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

4.默认配置端口为80，用户可以通过htttp://localhost访问；
端口配置文件位于standalone/configuration/standalone.xml（图中已经修改为8289）
![端口](http://upload-images.jianshu.io/upload_images/2572206-a6be597a2fb78f07.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

