title: 依赖扫描工具DependencyCheck
date: 2019-02-14T02:41:36.496Z
tags: [安全]
categories: [测试]
---
> 先上github地址：https://github.com/jeremylong/DependencyCheck
> DependencyCheck是一款可以扫描依赖工具的软件，使用起来很方便，但是报告比较难读


- 到`https://bintray.com/jeremy-long/owasp/dependency-check`下载最新的安装包
![](https://upload-images.jianshu.io/upload_images/2572206-b6ecc7a312da4bfd.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

<!--more-->

- 解压之后进入bin目录，默认有两个文件
![](https://upload-images.jianshu.io/upload_images/2572206-a1fd1ae7c3f9ddaa.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

- 将自己的项目打包成一个war包`test.war`
```
--project  项目名
--scan  扫描文件名
--out  报告输出地址

./dependency-check.bat --project test --scan test.war --out .
```

- 等一段时间就可以看到生成了报告文件
![](https://upload-images.jianshu.io/upload_images/2572206-dc4cb2b5db26b03d.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
![可以在列表里看到有漏洞的依赖名称](https://upload-images.jianshu.io/upload_images/2572206-55bff5546835ee17.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
![点进去之后就可以看到详细的漏洞报告](https://upload-images.jianshu.io/upload_images/2572206-568076f3b8f02f23.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

- 笔者看过自己项目的报告后发现，大部分的漏洞在特定场景下才会生效，但是为了避免万一，我们还是一劳永逸的升级一下jar包的版本






