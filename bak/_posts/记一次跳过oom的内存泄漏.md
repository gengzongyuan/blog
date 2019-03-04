title: 记一次跳过oom的内存泄漏
date: 2019-02-14 10:48:37
tags: [性能测试]
categories: [测试]
---
#### 整体复盘
- 18:23：数据同学告知图片拼接服务404
- 第一时间去瞄了日志，发现宕机前落了如下日志
![](https://upload-images.jianshu.io/upload_images/2572206-f8dd910e4afda90e.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
```
Java HotSpot(TM) 64-Bit Server VM warning: INFO: os::commit_memory(0x00007fb0eaa1b000, 12288, 0) failed; error='Cannot allocate memory' (errno=12)
```
<!--more-->

  - 第一反应去看了眼jvm堆信息，jvm各区正常

- 检查磁盘剩余容量，正常

- 查看服务器内存，剩余**7G**，到这里笔者就奇怪了，正常服务启动之后会剩余4G内存，看来服务彻底宕了

- 从运维平台的监控系统检查过去这段时间的内存分布，看到内存慢慢趋近于0，最后宕机
![](https://upload-images.jianshu.io/upload_images/2572206-cf8d43d1893b7b2e.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

- **重新拉起服务，开始压测**

- 内存跟之前一样
![内存情况](https://upload-images.jianshu.io/upload_images/2572206-d0f983c388b24285.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

- 压测过程中使用`jstack -pid`查看了线程栈状况

- 发现状况，线程栈堆积了大量线程，到这里思路清晰了，服务启动了太多线程，问了开发同学由于开启线程的时候调用的java native方法，线程使用的是操作系统内存，随着线程的增长，逐渐服务器内存不足，跳过OOM直接从操作系统层面报出了内存不足
![](https://upload-images.jianshu.io/upload_images/2572206-837eaee4b87ad9e9.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

- 重启服务后简单压测之后使用jmap dump下来堆信息，进行了分析，交给开发同学
![](https://upload-images.jianshu.io/upload_images/2572206-7fff81a75194dbf5.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

- 后续定位结果是图片对象没有及时回收导致持续堆积



