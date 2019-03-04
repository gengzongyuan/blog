title: 前端诊断与调优技巧（YSlow与PageSpeed）
date: 2018-09-20 17:47:07
tags: [性能测试,pageSpeed,yslow]
categories: [测试]
---


> 工具
- YSlow
- PageSpeed
[^-^]:- dynaTrace Ajax

<!--more-->

### 一、工具比较
|工具|优点|缺点|
|:-|:-|:-|
|YSlow|1.基于yahoo的23条准则进行分析<br>2.支持查看内网效率|纯英文|
|PageSpeed|1.支持中文<br>2.提供了基于Web服务器的模块<br>3.可分析手机页面速度|无法查看内网页面效率|
|dynaTrace Ajax|--|--|


### 二、工具使用与分析


#### 1.YSlow
YSlow是比较常见的性能监控插件，依赖于Firebug，是依托于yahoo的23条准则进行判断的。

**1.1安装** 
安装firebug
![firebug](http://upload-images.jianshu.io/upload_images/2572206-9497f6429bcb331b.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
安装Yslow
![Yslow](http://upload-images.jianshu.io/upload_images/2572206-baa47f1382da49aa.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

**1.2**使用
1.打开firebug
![打开firebug](http://upload-images.jianshu.io/upload_images/2572206-4364d507d40299af.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
2.查看YSlow，可以看到当前页面的整体评分，以及23条原则对应的打分
![YSlow](http://upload-images.jianshu.io/upload_images/2572206-98cf92d455950bef.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
3.每条打分对应的原因可以点击查看（可以看到当前gzip打分为F，具体原因及解释都有相应解释）
![](http://upload-images.jianshu.io/upload_images/2572206-1079188004a8e484.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
4.statistics界面提供了界面的缓存信息（左为无缓存时的大小，右为做缓存后页面的大小），便于分析有哪些内容可以使用本地缓存来降低对服务器的压力。
![](http://upload-images.jianshu.io/upload_images/2572206-3f1490368e1ad7d3.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

**2.3分析**
对页面的分析,YSlow已经给出相应建议

#### 2.PageSpeed
PageSpeed是Google开发的前端分析工具，使用简单，分析明确，**但是不支持内网页面**


**2.1 安装**
通过谷歌浏览器拓展程序下载对应插件即可
![安装](http://upload-images.jianshu.io/upload_images/2572206-4ec61d19b1c0649d.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

**2.2使用**
使用简单，直接把需要分析的页面链接放到数据框即可，下图为分析https://www.processon.com/的结果
![processon分析](http://upload-images.jianshu.io/upload_images/2572206-b3eb22ee930ec582.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

**2.3分析**
需要调优的内容已经很贴心的都进行了提示







> 参考
[Yslow-23条规则](http://www.cnblogs.com/lori/archive/2013/03/19/2969510.html)
[dynaTrace Ajax：前端性能分析利器](https://www.ibm.com/developerworks/cn/web/1205_xieju_dtraceajax/#artrelatedtopics)
