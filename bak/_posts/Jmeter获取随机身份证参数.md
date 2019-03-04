title: Jmeter获取随机身份证参数
date: 2018-09-21 16:34:29
tags: [jmeter,性能测试]
categories: [测试]
---


> 项目上遇到需要使用身份证信息进行注册，代码已经封在jar包里，各位同学可以参考下面步骤调用
下载链接：https://pan.baidu.com/s/1wQUoBz5GMh3JKxbpBs3qpQ 密码：ptdo
jmeter版本：4.0
jdk：1.8

<!--more-->

1.导入jar包![](https://upload-images.jianshu.io/upload_images/2572206-459ad3d38dad0d13.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


2.添加一个用户自定义的变量
![](https://upload-images.jianshu.io/upload_images/2572206-457e7987cf71b8f1.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![](https://upload-images.jianshu.io/upload_images/2572206-fb801b46dbe253a5.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

3.新建一个BeanShell，import对应类，并使用vars.put保存身份证
![](https://upload-images.jianshu.io/upload_images/2572206-9286ad5571dcddc1.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


代码如下：
import com.shenfen.IdCardGenerator;
vars.put("idCard",IdCardGenerator.getIdCard());
4. 在其他地方直接通过${idCard}调用身份证参数就好了

![](https://upload-images.jianshu.io/upload_images/2572206-f5dbceebd0f730b6.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

5.结果![](https://upload-images.jianshu.io/upload_images/2572206-a40a0315d96ea6fd.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
bingo

