title: YARN调度器
date: 2019-02-28 16:46:24
tags: [hadoop]
categories: [开发]
---
#### YARN调度器
```
FIFO  先进先出
Capacity  计算（默认使用）
| yarn.resourcemanager.scheduler.class | org.apache.hadoop.yarn.server.resourcemanager.scheduler.capacity.CapacityScheduler |

Fair  公平

生产常用Fair调度器
yarn-site.xml
```

<!--more-->


##### FIFO调度器
![](https://upload-images.jianshu.io/upload_images/2572206-af6a3ed12da68b06.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
```
特点：先进先出
- FIFO把作业按照提交顺序排成一个*先进先出*的队列，在进行资源分配的时候，先给队列最前的作业分配资源，待最前的作业满足资源需求后再给下一个分配。
- 不适用于共享集群：大任务可能会使用所有集群的所有资源，这就会导致其他的应用被阻塞
- 官网基本上没有fifo调度器的相关文档了，估计在某个版本已经弃用，这里我们做了解就好
```

##### Capacity调度器
![](https://upload-images.jianshu.io/upload_images/2572206-d2f1de403fd6a740.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
```
org.apache.hadoop.yarn.server.resourcemanager.scheduler.capacity.CapacityScheduler
特点：单独分配一个小队列执行小任务
- Capacity调度器会有一个专门的小队列运行小任务
- 会预先占用一定的集群资源，如果没有小任务那么这部分的资源就会被浪费掉
```

#### Fair调度器
![](https://upload-images.jianshu.io/upload_images/2572206-ce223bf4b83c1fba.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
```
org.apache.hadoop.yarn.server.resourcemanager.scheduler.fair.FairScheduler
特点：动态调整
- Fair调度器会为所有运行的job动态调整系统资源
- 如果只有一个任务被运行，会获得所有的集群资源
- 如果此时加入了第二个任务，fair调度器会分配一般的资源给第二个任务（这个过程是有一定延时的）
- 待第二个任务运行完毕，释放资源给第一个任务继续使用
```