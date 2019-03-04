title: loadRunner参数化之参数空值
date: 2018-09-20 17:44:29
tags: [性能测试,loadrunner]
categories: [测试]
---


## 如何参数化空值

> 在一些情况下（搜索时参数为空则全部搜索），我们需要设置搜索值为空值，那么我们又该如何设置呢

### 实验步骤

<!--more-->

----
#### 1.Action demo


```
Action()
{
	lr_error_message("param1 is:-%s-",lr_eval_string("{param1}"));
	return 0;
}
```

#### 2.设置Run-Times Settings
![](http://upload-images.jianshu.io/upload_images/2572206-4d6479090b007edd.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)



#### 3.设置参数化

设置参数param1，对data file编辑如图（第二行直接回车，第四行为空格）设置
![](http://upload-images.jianshu.io/upload_images/2572206-e64d31bccf7bc1ed.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
我们可以观察到，第二行的数据在参数化视图中并没有显示，那么运行时loadrunner是否也不使用第二行的数据呢？

#### 4.执行结果
![](http://upload-images.jianshu.io/upload_images/2572206-08d1d6e66f23729d.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)



> 通过执行结果，我们可以看到，loadrunner在第二次迭代时，仍然使用了第二行的空值，但是由于loadrunner参数化的设置中却并不显示


