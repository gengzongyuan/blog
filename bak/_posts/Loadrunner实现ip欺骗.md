title: Loadrunner实现ip欺骗
date: 2018-09-20 17:45:25
tags: [性能测试,loadrunner]
categories: [测试]
---


在性能测试过程中，为了满足一些特定的场景：**频繁访问**、**负载均衡**或**访问受限的场景**，就需要运用ip欺骗了。

**1、**  录制脚本，简单录制一个发帖的脚本。

<!--more-->

**2、** 把脚本放场景中来，脚本是脚本，场景是场景，一定要理解清楚两者的关系。欺不欺骗的就在场景中设置。看图，菜单：场景-启用IP欺骗器。
![启用ip欺骗](http://upload-images.jianshu.io/upload_images/2572206-e7e277d90279192c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

勾选菜单：Tools-Expert Mode
![勾选Expert Mode](http://upload-images.jianshu.io/upload_images/2572206-9659855edf786581.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

打开Tools里的options，确保general中的IP address mode和runtime setting中一致
![IP address mode](http://upload-images.jianshu.io/upload_images/2572206-d31ebde54766214f.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

**3、**IP欺骗的前提条件是本机的IP地址是写死的，不是自动获取的
![手动配置IP](http://upload-images.jianshu.io/upload_images/2572206-29e158b0ea442b20.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

该设置些IP了，在哪里设置呢，在这里见下图。
![打开IP向导](http://upload-images.jianshu.io/upload_images/2572206-53fc23d34a3e8539.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

进入IP向导第1步，默认选项“创建新位置”，点击下一步；

![创建新位置](http://upload-images.jianshu.io/upload_images/2572206-2749e17536687dc9.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

进入IP向导第2步，需选择当前网络的网卡

![选择网卡](http://upload-images.jianshu.io/upload_images/2572206-842bb1f99047c347.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

进入IP向导第2步，需输入服务器的IP地址，原理上该地址是和你录制脚本的服务器地址一致的

![配置目标服务器IP](http://upload-images.jianshu.io/upload_images/2572206-f9e783a4f7439ccd.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


到了这里就可以开始添加要配的ip了，点击Add
![add](http://upload-images.jianshu.io/upload_images/2572206-79e4fdca98cf3791.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


选择开始的ip和数量，这里从192.168.1.200开始，生成了10个ip

![](http://upload-images.jianshu.io/upload_images/2572206-01a25c29a32976ba.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![](http://upload-images.jianshu.io/upload_images/2572206-1e8114e3c7fec429.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


值得一提的是，如果添加的ip被检测到有冲突，会提示
![](http://upload-images.jianshu.io/upload_images/2572206-9d29c22ebedbb9d0.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![](http://upload-images.jianshu.io/upload_images/2572206-73aa2f099d85093e.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

**4、**执行起来到日志里看看有没有成功吧。
![image.png](http://upload-images.jianshu.io/upload_images/2572206-452a58404bbff8c7.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


