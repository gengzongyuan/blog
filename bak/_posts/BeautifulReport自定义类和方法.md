title: BeautifulReport自定义类和方法
date: 2019-02-26T08:03:44.900Z
tags: [自动化]
categories: [测试]
---
> 前言：自动化测试的报告里，一直都是用的BeautifulReport，但是测试类和测试方法没办法自己定义是硬伤，但是又想使用BeautifulReport，笔者读了BeautifulReport的源码后进行了改动

#### 效果

- 没经过改造前的BeautifulReport报告
![](https://upload-images.jianshu.io/upload_images/2572206-f09542f9fcce852e.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

<!--more-->

- 改造后的BeautifulReport报告（可以自己定义）
![](https://upload-images.jianshu.io/upload_images/2572206-810217008cc5be49.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

#### 改造方式

- 修改代码
```
BeautifulReport.py文件

    @staticmethod
    def get_testcase_property(test) -> tuple:
        """
            接受一个test, 并返回一个test的class_name, method_name, method_doc属性
        :param test:
        :return: (class_name, method_name, method_doc) -> tuple
        """
        # 原代码
        # class_name = test.__class__.__qualname__

        # 为了做自动化修改了代码
        interfaceName = test.__dict__['_testMethodInterface']
        method_name = test.__dict__['_testMethodName']

        method_doc = test.__dict__['_testMethodDoc']
        return interfaceName, method_name, method_doc


用过BeautifulReport的朋友应该知道可以通过_testMethodDoc传递一个测试描述进去
这里我们将原来的类名和方法变为也可以自己指定的方式，来替换原来的方式
```
```
这样我们在建case的时候，只要指定好对应的参数，就会赋给对应的值

    def test567(self):
        self._testMethodName = "冒烟"
        self._testMethodInterface = "/personPackage/add"
        self._testMethodDoc = "新建人群包_自主上传_IMEIMD5"
```
- 修改html模板
```
\site-packages\BeautifulReport\template

template存储了我们报告里的模板
想要修改对应的文案，直接在里边修改就可以了
```