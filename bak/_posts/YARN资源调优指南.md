title: YARN资源调优指南
date: 2019-02-28 16:07:23
tags: [hadoop]
categories: [开发]
---
#### 内存资源规划与配置
- 内存资源规划
```
48G物理内存

- linux系统本身需要占用的内存+空留：20%-25%  9.6G
- 剩余：80%  38.4G

生产情况，NN和RM是部署在一台机器上的，一般分配8个G的内存，看情况适当调大
- NN：8G
- RM：8G

生产情况，DN和NM也是部署在一台机器上的
数据本地化：减少网络IO
- DN：4G
- NM：4G

剩余资源内存=30.4G，运行container容器（每一个container容器都是一个单独的进程）
```

<!--more-->

- hdfs:NN和DN进程配置
```
  hadoop-env.sh（参数需要自己添加）
  # NN内存
  HADOOP_NAMENODE_OPTS=-Xmx1000m（默认）
  HADOOP_NAMENODE_OPTS=-Xmx8192m（建议8G）
  # DN内存
  HADOOP_DATANODE_OPTS=-Xmx1000m（默认）
  HADOOP_DATANODE_OPTS=-Xmx4096m（建议4G）
```
- yarn:RM和NM进程配置
```
默认1000M
  yarn-env.sh
  # RM内存
  export YARN_RESOURCEMANAGER_HEAPSIZE=1000（默认）
  export YARN_RESOURCEMANAGER_HEAPSIZE=8192（建议8G）
  # NM内存
  export YARN_NODEMANAGER_HEAPSIZE=1000（默认）
  export YARN_NODEMANAGER_HEAPSIZE=4096（建议4G）
```
- container容器进程配置
```
yarn-site.xml

  # container总内存
  yarn.nodemanager.resource.memory-mb   -1（默认8G）
  yarn.nodemanager.resource.memory-mb   30*1024（总内存-【系统+空留内存】-【NN内存】-【NM内存】）
  # 单个container最小内存
  yarn.scheduler.minimum-allocation-mb  1024（默认）
  yarn.scheduler.minimum-allocation-mb  2048（建议2G）
  # 单个container最大内存
  yarn.scheduler.maximum-allocation-mb  8192 （默认8G）
  yarn.scheduler.maximum-allocation-mb  8192 （建议和yarn.nodemanager.resource.memory-mb配置成一样的大小）

如果单个container任务需要到9G的内存，但是我们最大内存仅配置了8G，那么将会抛出InvalidResourceRequestException错误

yarn.scheduler.maximum-allocation-mb的配置同时也需要参考生产的任务，如果没有特别大的任务，可以适量减少，这里配置和yarn.nodemanager.resource.memory-mb一样大小不是固定调优手段
适当减少的时候需要注意，一定要yarn.nodemanager.resource.memory-mb/yarn.scheduler.maximum-allocation-mb是一个整数，否则会有剩余内存空出
```
- container OOM是否主动kill配置
```
- 保持默认，不要动这三个参数
# 物理内存不足时是否主动kill掉contailer进程
yarn.nodemanager.pmem-check-enabled	true
# 虚拟内存不足时是否主动kill掉contailer进程
yarn.nodemanager.vmem-check-enabled	true
# 允许使用的物理内存和虚拟内存比率
yarn.nodemanager.vmem-pmem-ratio	2.1
```

#### CPU资源规划与配置
```
# 物理core和vcore的比率
yarn.nodemanager.resource.pcores-vcores-multiplier  1.0（最新版本改为1.0了，之前是2.0，所以我们在修改下面可以使用的vcore时，先计算一下当前的总vcore是多少）

# NM可以使用的所有的vcore
yarn.nodemanager.resource.cpu-vcores  8（默认）
yarn.nodemanager.resource.cpu-vcores  X（按照当前总vcore*80%，不要把所有的vcore拿过来用）

# 每个容器可以使用的最小vcore
yarn.scheduler.minimum-allocation-vcores  1（默认）
yarn.scheduler.minimum-allocation-vcores  1
# 每个contailer可以使用的最大vcore
yarn.scheduler.maximum-allocation-vcores  4（默认）
yarn.scheduler.maximum-allocation-vcores  4
```
#### 总结
```
yarn的在调优时候要综合考虑CPU和内存的分配，尽量保证不要空出多余的资源
假如container总内存30，container最小2G，总vcore 8 container最小1c
那么我们内存可以启动最多15个container，cpu最多启动8个container
最终可以启动的最多container就是8个，会有相当多的内存没有使用到
所以生产上的调优配置需要综合考量内存和cpu的配比

同时如果机器上也部署了其他服务，hbase,kafka我们就要考虑空出其他的内存给对应的部分
```


> 参考博客http://blog.itpub.net/30089851/viewspace-2127851/









