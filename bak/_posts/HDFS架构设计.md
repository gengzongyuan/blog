title: HDFS架构设计
date: 2019-02-20 19:38:36
tags: [hadoop]
categories: [开发]
---
#### 块剖析
- 配置信息
```
块的大小128M 
块的副本数3 
<property>
        <name>dfs.blocksize</name>
        <value>134217728</value>
</property>
<property>
        <name>dfs.replication</name>
        <value>3</value>
</property>

blocksize和replication参数解读

一个文件260M会按照128M的规格切分成:128+128+4的三块
这三块数据，每块数据都有3份，分布在不同的dn上

共计：9块数据，总大小260*3大小
```

<!--more-->

#### 架构设计
- 架构图
![](http://upload-images.jianshu.io/upload_images/2572206-fdee0ba4c84ff580.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
- nn
```
nn: 文件系统的命名空间
文件名称
文件目录结构
文件属性 创建时间 权限 副本数
文件对应哪些数据块
-->数据块对应哪些datanode节点上

- nn维护的blockmap:
nn节点不会持久化存储这种映射关系
dn定期发送blockreport 给nn,
以此nn在【内存】中动态维护这种映射关系！

- 持久化数据
作用:
管理文件系统的命名空间，
维护文件系统树，以两种文件永久保存在磁盘上
命名空间镜像文件 fsimage
编辑日志  editlog
[root@hadoop001 current]# pwd
/tmp/hadoop-root/dfs/name/current
[root@hadoop001 current]# ll
total 1040
-rw-r--r--. 1 root root 1048576 Feb 17 20:23 edits_inprogress_0000000000000000001
-rw-r--r--. 1 root root     321 Feb 17 19:23 fsimage_0000000000000000000
-rw-r--r--. 1 root root      62 Feb 17 19:23 fsimage_0000000000000000000.md5
-rw-r--r--. 1 root root       2 Feb 17 19:23 seen_txid
-rw-r--r--. 1 root root     219 Feb 17 19:23 VERSION
```
- dn
```
存储: 数据块 和数据块的校验和
与nn通信:
- 每隔3秒发送一个心跳,hadoop001:50070上的datanode可以看到上次通信时间每3秒刷新一次
- 每10次心跳发送一次当前节点的blockreport
作用: 读写文件的数据块
```
- hdfs不适合存储小文件的原因？
```
由于nn是在内存中维护映射关系的，所以如果小文件特别多的时候，内存很容易爆
- 解决方式：合并，细节后续拓展
```
- snn
```
存储: fsiamge+editlog
作用: 定期合并fsimage+editlog文件为新的fsimage文件
	推送nn节点，简称为检查点  checkpoint
参数 ：
dfs.namenode.checkpoint.period  3600s 

由于SNN只能备份上一小时之前的数据，这一小时的数据不会存储，所以生产环境上，一般还会采用其他方式进行热备份
```
- 官网建议
```
A typical deployment has a dedicated machine that runs only the NameNode software. 
Each of the other machines in the cluster runs one instance of the DataNode software.
The architecture does not preclude running multiple DataNodes on the same machine 
but in a real deployment that is rarely the case.
建议：NN和DN是部署在不同的节点上
```