title: Hadoop入门介绍
date: 2019-02-15T10:19:44.346Z
tags: [hadoop]
categories: [开发]
---
#### Hadoop概述
- Hadoop名字的由来
```
- 作者：Nutch、Doug Cutting
- 是一个虚构的名词
- 项目作者的孩子给一个棕色的大象样子的填充玩具的命名
```
#### 学习方法
```
学习一个新的框架，直接查看该项目的官网地址
Hadoop
Hive

Apache社区
hadoop.apache.org
hive.apache.org
spark.apache.org
flink.apache.org

官网释义：
reliable, scalable, distributed computing.
```

<!--more-->

#### Hadoop核心组件
- 核心组件总述
```
## Modules

The project includes these modules:

*   **Hadoop Common**: The common utilities that support the other Hadoop modules.
*   **Hadoop Distributed File System (HDFS™)**: A distributed file system that provides high-throughput access to application data.
*   **Hadoop YARN**: A framework for job scheduling and cluster resource management.
*   **Hadoop MapReduce**: A YARN-based system for parallel processing of large data sets.
*   **[Hadoop Ozone](https://hadoop.apache.org/ozone/)**: An object store for Hadoop.

Hadoop：提供分布式的存储（一个文件被拆分成很多个块、并且以副本的方式存储在各个节点中）和计算
  - 是一个分布式的系统基础架构：用户可以在不了解分布式底层细节的情况下进行使用

分布式文件系统：hdfs实现将文件分布式存储在很多服务器上
分布式计算框架：MapReduce实现在很多机器上分布式并行计算
分布式资源调度框架：YARN实现集群资源管理以及作业的调度

```
- HDFS
```
源自Google的gfs论文
hdfs是gfs的克隆版：拓展性、容错性、海量数据存储

- 将文件切分成指定大小的数据块并以多副本的形式存储在各个机器上
文件  test.log  200M
块（block）：默认的block size是128M,将会分成2个块：128M+72M   blk1 + clk2
有五台机器
node1: blk1 blk2
node2:blk2
node3:blk1 blk2
node4:blk1
node5:
假如有一台节点挂掉，由于副本的存在，并不会产生影响

- 数据的切分、多副本、容错等操作对用户是透明的（用户是不感知的）
```
![案例](https://upload-images.jianshu.io/upload_images/2572206-c5cf8b69ef2aed1b.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

- MapReduce
```
源自google的MapReduce论文
MapReduce是google论文的克隆版本：拓展性、容错性、海量数据离线处理
```
![](https://upload-images.jianshu.io/upload_images/2572206-dfff7d71f4984a66.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
```
hadoop官网的wc案例
input：数据输入
splitting：切割
mapping：分组
shuffing：聚合
reducing：统计结果
final result：输出最终结果
```
- YARN：资源调度框架
```
YARN:Yet Another Resource Negotiator
负责整个集群资源的管理和调度
YARN特点：拓展性、容错性、*多框架资源统一调度*（hive、spark都是可以使用yarn的资源调度的）
```
![](https://upload-images.jianshu.io/upload_images/2572206-5ceda039600467bc.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

#### Hadoop优势
```
高可靠性
  数据存储：数据块多副本
  数据计算：重新调度作业计算
扩展性
  存储/计算资源不够时，可以横向的线性拓展机器
  一个集群中可以包含数以千计的节点
其他
  成本低：存储在廉价的机器上
  去IOE：IBM的小型机、Oracle数据库、EMC存储设备
  成熟的生态圈
```
#### Hadoop发展史
- 参考文章：`https://www.infoq.cn/article/hadoop-ten-years-interpretation-and-development-forecast`
#### Hadoop生态系统
```
- 狭义：是一个适合大数据**分布式存储**，**分布式计算**和**资源调度**的平台
- 广义：Hadoop生态系统，**Hadoop生态系统**是一个很庞大的概念，Hadoop是其中最重要最基础的一个部分，生态系统中每一个子系统**只解决某一特定的问题域**，不搞统一性的全能系统，而是**小而精**的多个小系统
```
![生态环境](https://upload-images.jianshu.io/upload_images/2572206-ad33af95780e2193.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
```
- 特点
  - 开源、社区活跃
  - 囊括了大数据处理的方方面面
  - 成熟的生态圈
```

#### Hadoop发行版的选择
```
常用的hadoop的发行版
  Apache-不建议
    纯开源
    不同版本/框架之间整合 jar包冲突....吐血

  CDH：https://www.cloudera.com/  国内60%-70%
    优点：cm（cloudera manager）通过页面一键安装各种安装框架、升级
    缺点：cm不开源、与社区版本有些许出入

  Hortonworks：HDP  企业发布自己的数据平台可以直接基于页面框架进行改造
    优点：原装hadoop、纯开源、支持tez
    缺点：企业级安全不开源

  MapR：不推荐
```