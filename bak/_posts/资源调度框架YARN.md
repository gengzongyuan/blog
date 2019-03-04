title: 资源调度框架YARN
date: 2019-02-20 13:40:08
tags: [hadoop,yarn]
categories: [开发]
---
> YARN :Yet Another Resource Negotiator(另外一种资源管理器)


#### YARN产生背景
- MapReduce1.x存在的问题（JobTracker是单节点）
![](https://upload-images.jianshu.io/upload_images/2572206-6e101a140e53cf77.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

<!--more-->

- 资源利用率&运维成本（一种类型一个集群）





#### YARN架构
- yarn架构设计
```
mr job执行流程
mr on yarn架构
yarn架构设计

> 三个问题全部对应一个问题
```

- 1 RM(ResourceManager) + N NM(NodeManager)  一主多被
![](https://upload-images.jianshu.io/upload_images/2572206-6fcabdedd2559f08.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

- ResourceManager
```
  - 一个集群avtive状态的RM只有一个，负责整个集群的资源管理和调度
  - 处理客户端请求（启动/杀死）
  - 启动/监控ApplicationMaster（一个作业对应一个AM）
  - 监控NodeManager， 如果NM挂掉，告诉对应的ApplicationMaster（非次NM的AM）如果处理
  - 系统的资源分配和调度
```

- NodeManager
```
  - 通过心跳定期向RM汇报本节点的资源使用、任务运行和Container运行情况
  - 接收RM Container的启动和停止（看图AM是启动在Container上的）
  - 单个节点的资源管理和任务管理
```

- AM（AplicationMaster）
```
  - 每一个应用/作业对应一个AM，负责应用程序的管理
  - 数据的切分
  - 为应用程序向RM申请资源(container)，并分配给内部任务
  - 与NM通信，以启停task,task是运行在container中的
  - task的监控和容错
```
###### YARN - Container
- vcore概念
```
YARN中目前的CPU被划分成虚拟CPU（CPU virtual Core），
- YARN自己引入的概念，
- 初衷是，考虑到不同节点的CPU性能可能不同，每个CPU具有的计算能力也是不一样的，

用户提交作业时，可以指定每个任务需要的虚拟CPU个数。

历史版本中，默认1物理core=2vcore
最新版本，1物理core=1vcore
```
- 容器
```
YARN中目前的

- Container是运行在nodemanager节点机器上，虚拟的概念
- 将一定大小的内存和cpu vcore组成的最小单元
- 运行计算的任务task，每1个container都是1个单独的进程
```


#### YARN的执行流程
![](https://upload-images.jianshu.io/upload_images/2572206-84c748c6bb866415.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

```
ResourceManager 资源作业管理者  
	Applications Manager 作业管理
	Resource Scheduler 资源调度

NodeManager   节点者

- 1. client向RM提交作业（job），其中包括applicationsMaster程序、启动applicationMaster命令等
- 2. RM为该job分配第一个容器，并与对应的NM通信，要求它在这个容器中启动MR applicationMaster程序
- 3. applicationMaster首先向application Manager注册，此时用户就可以在web界面查看job的整个运行状态
- 4. applicationMaster和Resource Scheduler通信，采用轮询的方式通过RPC协议去申请和领取资源列表
- 5. applicationMaster申请到资源后，便于对应的NM通过RPC通信，要求启动任务
- 6. NodeManager为任务设置好运行环境（包括环境变量、 JAR包、二进制程序等）后，将任务启动命令写到一个脚本中，并通
过运行该脚本启动任务
- 7. 各个任务通过某个RPC协议向ApplicationMaster汇报自己的状态和进度，以让ApplicationMaster随时掌握各个任务的运行
状态，从而可以在任务失败时重新启动任务。在应用程序运行过程中，用户可随时通过RPC向ApplicationMaster查询应用程序
的当前运行状态
- 8. 应用程序运行完成后， ApplicationMaster向ResourceManager注销并关闭自己

简述：
a. 第一个阶段是启动ApplicationMaster；
b. 第二个阶段是由ApplicationMaster创建应用程序，为它申请资源，并监控它的整个运行过程，直到运行完成。
```













































