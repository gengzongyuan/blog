title: MediaWiki系统搭建（类维基百科系统）
date: 2018-09-20 16:49:45
tags: [MediaWiki]
categories: [运维]
---


### [mediawiki在windows下的安装](http://www.cnblogs.com/edwardsun/p/3904304.html)

> 文件下载地址：https://pan.baidu.com/s/1eUdDhlw 密码：xvhb
环境：
>- windows7
> - wamp 

<!--more-->

### 安装与使用
- 1. 下载完毕后会有两个文件，wamp环境安装包、mediaWiki安装包
![下载](http://upload-images.jianshu.io/upload_images/2572206-5913de48d2552739.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

- 2. 执行wamp安装包，安装完毕后执行可以看到
![](http://upload-images.jianshu.io/upload_images/2572206-accef2055d6bc80f.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


- 3. 访问http://localhost（默认端口为80），可以看到Apache、PHP、MySql、MariaDB版本号（MySql数据库默认密码为空）
![image.png](http://upload-images.jianshu.io/upload_images/2572206-2b99013b84c86860.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

- 4. 打开C:\wamp64\bin\apache\apache2.4.27\conf\httpd.conf对相应端口进行修改（默认为80）
```
...
Listen 0.0.0.0:8098
Listen [::0]:8098
...
ServerName localhost:8098
```

- 5. 将下载的mediaWiki压缩包解压后，放置在wamp64\www目录下

- 6. 浏览器访问http://localhost:8098/mediawik （未做端口修改，则默认不用加端口），按照MediaWiki的提示进行安装。
![安装成功](http://upload-images.jianshu.io/upload_images/2572206-73481fd349168f69.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

- 7. 笔者后续遇到localhost访问没问题，但是别人却无法访问的现象，解决方式请查看[Apache 403 错误解决方法-让别人可以访问你的服务器](http://www.cnblogs.com/mrlaker/archive/2013/04/29/3050888.html)

![](http://upload-images.jianshu.io/upload_images/2572206-802c78e7df941ae2.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)



> 参考文章
[MediaWiki初探：安装及使用入门](http://blog.csdn.net/wangnan537/article/details/37743497)
[mediawiki在windows下的安装](http://www.cnblogs.com/edwardsun/p/3904304.html)
[Apache 403 错误解决方法-让别人可以访问你的服务器](http://www.cnblogs.com/mrlaker/archive/2013/04/29/3050888.html)


