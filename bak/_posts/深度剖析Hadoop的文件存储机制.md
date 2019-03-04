title: 深度剖析Hadoop的文件存储机制
date: 2019-02-20 19:38:57
tags: [hadoop]
categories: [开发]
---
```
- 上传一个比较大的文件，就拿我们Hadoop的压缩包进行上传
hadoop fs -put hadoop-2.6.0-cdh5.7.0.tar.gz /

上传完毕之后到浏览器页面查看一下文件
```
![](https://upload-images.jianshu.io/upload_images/2572206-cee742faa9c36620.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

<!--more-->

- 点开文件，可以看到三个块
![](https://upload-images.jianshu.io/upload_images/2572206-f8a48d49f714f877.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
```
可以看到三个Block ID分别是
Block ID: 1073741829
Block ID: 1073741830
Block ID: 1073741831

那么这三个Block存放在哪个位置呢
进到我们之前配置的tmp目录，一路往下跟
/app/tmp/dfs/data/current/BP-1941274515-192.168.142.128-1547548761994/current/finalized/subdir0/subdir0

可以看到对应的三个ID的文件已经在文件夹中了
[root@hadoop001 subdir0]# ls -l -h
total 300M
-rw-r--r--. 1 root root 1.4K Jan 15 03:12 blk_1073741825
-rw-r--r--. 1 root root   19 Jan 15 03:12 blk_1073741825_1001.meta
-rw-r--r--. 1 root root  17K Jan 15 03:16 blk_1073741826
-rw-r--r--. 1 root root  143 Jan 15 03:16 blk_1073741826_1002.meta
-rw-r--r--. 1 root root  101 Jan 15 03:18 blk_1073741827
-rw-r--r--. 1 root root   11 Jan 15 03:18 blk_1073741827_1003.meta
-rw-r--r--. 1 root root 1.4K Jan 15 03:25 blk_1073741828
-rw-r--r--. 1 root root   19 Jan 15 03:25 blk_1073741828_1004.meta
-rw-r--r--. 1 root root 128M Jan 15 03:40 blk_1073741829
-rw-r--r--. 1 root root 1.1M Jan 15 03:40 blk_1073741829_1005.meta
-rw-r--r--. 1 root root 128M Jan 15 03:40 blk_1073741830
-rw-r--r--. 1 root root 1.1M Jan 15 03:40 blk_1073741830_1006.meta
-rw-r--r--. 1 root root  42M Jan 15 03:40 blk_1073741831
-rw-r--r--. 1 root root 330K Jan 15 03:40 blk_1073741831_1007.meta

HDFS存储拓展：
	put: 1file ==> 1...n block ==> 存放在不同的节点上的
	get: 去nn上查找这个file对应的元数据信息
	了解底层的存储机制这才是我们真正要学习的东西，掌握API那是毛毛雨

```

