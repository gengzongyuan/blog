title: Hadoop之jps的真相
date: 2019-02-20 13:39:51
tags: [hadoop]
categories: [开发]
---
##### 程序pid文件路径
- 通过jps可以查看到我们启动的nn snn dn进程
```
[hadoop@hadoop001 hsperfdata_hadoop]$ jps
21552 DataNode
21712 SecondaryNameNode
21431 NameNode
22094 Jps
```

<!--more-->

- 如果没有修改过配置，默认是存到/tmp目录
```
[hadoop@hadoop001 hsperfdata_hadoop]$ pwd
/tmp/hsperfdata_hadoop
**注意这里的文件名称_后面跟的是启动用户

[hadoop@hadoop001 hsperfdata_hadoop]$ ll
total 96
-rw-------. 1 hadoop hadoop 32768 Feb 19 06:56 21431
-rw-------. 1 hadoop hadoop 32768 Feb 19 06:56 21552
-rw-------. 1 hadoop hadoop 32768 Feb 19 06:56 21712
```

- 为了避免/tmp的数据被清掉，对程序造成影响，我们一般会修改pid的文件路径
```
hadoop-env.sh

- pid文件目录
export HADOOP_PID_DIR=/home/hadoop/app/tmp
```

##### jps看到的情况真的是对的吗？
- 以hadoop启动的进程，以root身份jps查看
```
[root@hadoop002 ~]# jps
1520 Jps
1378 -- process information unavailable
1210 -- process information unavailable
1086 -- process information unavailable

可以看到process information unavailable
```
- 此时我们其实是不知道进程是否继续跑着的，所以这里记住一点
```
ps -ef|grep namenode 天下第一
```