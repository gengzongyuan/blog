title: python运行失败 报错：api-ms-win-core-path-l1-1-0.dll
date: 2018-09-20 17:24:10
tags: [python]
categories: [开发]
---

> 前言：网上解决方案看了很多，本文针对其中一种情况，重新安装vc++2015
> 文件下载链接：https://pan.baidu.com/s/1-I2nhWwyOhBBEkgQngj6HA 密码：rel3

- 1. 首先执行一下下载的vc_redist.2015.x64.exe文件，1) 如果提示已经安装，点击卸载，然后重新安装  2)安装，不出意外，你应该会安装失败

- 2. 下面我们来解决一下vc_redist.2015安装失败的问题，打开控制面板
![控制面板](http://upload-images.jianshu.io/upload_images/2572206-3f2da247fb24c600.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


<!--more-->

- 3.查看已安装的更新，搜索KB2999
![](http://upload-images.jianshu.io/upload_images/2572206-c402a02ae816ae7e.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
![](http://upload-images.jianshu.io/upload_images/2572206-b41ff2927b4ce981.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

- 4. 如果你搜索到了，右键卸了它；如果你没搜到，查看第5步
    1) 访问C:\ProgramData\Package Cache文件夹，直接地址框进入，默认你是看不到文件夹的
    1) 右上角搜索.msu
![](http://upload-images.jianshu.io/upload_images/2572206-4516a15e8db9f0a7.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
    1) 安装它，然后重启。重新启动的时候会有一次准备配置windows提示，平时更新都会看到这个界面。
    1) 重新安装vc_redist.2015试试，成功了的话，那么恭喜你，你的python已经可以用了

- 5. 如果你没有在更新列表里搜到KB2999这条更新
    - 1.访问C:\ProgramData\Package Cache文件夹
![](https://upload-images.jianshu.io/upload_images/2572206-e00c4f0846a920d9.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
    - 2. 搜索.msu文件，如果没搜到，在笔者的网盘里下载一个
![](https://upload-images.jianshu.io/upload_images/2572206-31424c970644817c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
    - 3. 把Windows6.1-KB2999226-x64.msu文件重命名为Windows6.1-KB2999226-x64.cab
    - 4. 解压Windows6.1-KB2999226-x64.cab得到
![](https://upload-images.jianshu.io/upload_images/2572206-54d89b9861f581f2.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
    - 5.按住shift右键打开cmd
![](https://upload-images.jianshu.io/upload_images/2572206-6e7ccee37c1657e0.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
    -  6. 执行```dism /online /add-package /packagepath:"Windows6.1-KB2999226-x64.cab"```

- 6. 到这里重新安装一下vc_redist.2015.x64.exe文件，这次就可以成功了

- 7. 重新安装一下python3.6（网盘已附带），愉快的开始你的python之旅吧

