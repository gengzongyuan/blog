title: loadrunner+java怎么玩？
date: 2019-02-14 10:51:44
tags: [性能测试,loadrunner]
categories: [测试]
---
> 前言：工作上遇到部分参数需要通过CA服务器加密，加密需要调用CA jar包的方法，调用java代码是免不了的了。笔者公司有要求一定要用Loadrunner做，这种情况其实还是建议使用Jmeter的。废话不多说了，开始吧。

-------
环境：
win7+loadrunner11+jdk1.6
-------

#### 1、前期准备
Loadrunner11支持jdk的最高版本是1.6，使用```java -version```确定一下自己的jdk版本
![](https://upload-images.jianshu.io/upload_images/2572206-d2d302feda815e29.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


<!--more-->


#### 2、新建脚本
新建Java Vuser脚本
![](https://upload-images.jianshu.io/upload_images/2572206-3425f210756adb8a.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


进入runtime-setting
![](https://upload-images.jianshu.io/upload_images/2572206-be2b0c52638f0bd2.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


配置jdk目录，默认安装目录填```C:\Program Files (x86)\Java\jdk1.6.0_39```
![](https://upload-images.jianshu.io/upload_images/2572206-248a9fbef3306308.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

在action里输入一行java代码运行起来看看环境是否配置成功了```System.out.println("dashu");```
![](https://upload-images.jianshu.io/upload_images/2572206-f91d0a931f0e5696.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
![](https://upload-images.jianshu.io/upload_images/2572206-178cd85e853b7ece.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

#### 3、 调试java代码
我们要确保代码是可以执行的，笔者建议代码先封装好之后，直接在loadrunner中进行调用





