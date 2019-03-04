title: Hadoop案例wordcount实践
date: 2019-02-20 13:41:10
tags: [hadoop]
categories: [开发]
---
#### 提交MR作业到yarn上运行：wc
> 官方代码:`/app/hadoop-2.6.0-cdh5.7.0/share/hadoop/mapreduce`

- 启动jar包
```
cd /home/hadoop/app/hadoop-2.6.0-cdh5.7.0/bin

./hadoop jar /home/hadoop/app/hadoop-2.6.0-cdh5.7.0/share/hadoop/mapreduce/hadoop-mapreduce-examples-2.6.0-cdh5.7.0.jar


直接启动启动不起来，提示我们需要输入参数wordcount
**************
  wordcount: A map/reduce program that counts the words in the input files.
  wordmean: A map/reduce program that counts the average length of the words in the input files.
  wordmedian: A map/reduce program that counts the median length of the words in the input files.
  wordstandarddeviation: A map/reduce program that counts the standard deviation of the length of the words in the input files.
***************
```

<!--more-->

- 创建一个hello.txt文件并上传
```
hello.txt
hello	world	welcome
hello	welcome	

上传hello.txt文件到hdfs中
- hadoop fs -put hello.txt -p /input/wc/

查看文件是否上传成功
- hadoop fs -text /input/wc/hello.txt

*************
18/12/06 12:14:20 WARN util.NativeCodeLoader: Unable to load native-hadoop library for your platform... using builtin-java classes where applicable
hello	world	welcome
hello	welcome	
*************
```

- 执行hadoop wc任务
```
./hadoop jar /home/hadoop/app/hadoop-2.6.0-cdh5.7.0/share/hadoop/mapreduce/hadoop-mapreduce-examples-2.6.0-cdh5.7.0.jar wordcount /input/wc/hello.txt /output/wc/

等待任务执行结果
结束之后hadoop fs -ls /output/wc/ 

可以看到我们的输出结果已经在hdfs中产生了输出结果
***********
18/12/06 12:17:16 WARN util.NativeCodeLoader: Unable to load native-hadoop library for your platform... using builtin-java classes where applicable
Found 2 items
-rw-r--r--   1 root supergroup          0 2018-12-06 12:16 /output/wc/_SUCCESS
-rw-r--r--   1 root supergroup         26 2018-12-06 12:16 /output/wc/part-r-00000
***********

```
- 查看一下输出的值
```
hadoop fs -cat /output/wc/part-r-00000
******************
18/12/08 18:34:11 WARN util.NativeCodeLoader: Unable to load native-hadoop library for your platform... using builtin-java classes where applicable
hello	2
welcome	2
world	1
******************
```

#### 结合日志回顾任务执行过程
```
18/12/08 18:30:16 WARN util.NativeCodeLoader: Unable to load native-hadoop library for your platform... using builtin-java classes where applicable
****连接到ResourceManager
18/12/08 18:30:17 INFO client.RMProxy: Connecting to ResourceManager at /0.0.0.0:8032
****FileInputFormat读取文件进行拆分，日志可以看到读取之后将hello.txt拆分成了一个文件
18/12/08 18:30:22 INFO input.FileInputFormat: Total input paths to process : 1
18/12/08 18:30:22 INFO mapreduce.JobSubmitter: number of splits:1
****生成job并且执行，日志给了我们一个web地址可以查看执行的任务
18/12/08 18:30:23 INFO mapreduce.JobSubmitter: Submitting tokens for job: job_1544126256129_0002
18/12/08 18:30:24 INFO impl.YarnClientImpl: Submitted application application_1544126256129_0002
18/12/08 18:30:24 INFO mapreduce.Job: The url to track the job: http://hadoop001:8088/proxy/application_1544126256129_0002/
****正式执行任务，执行过程:map->reduce
18/12/08 18:30:24 INFO mapreduce.Job: Running job: job_1544126256129_0002
18/12/08 18:30:56 INFO mapreduce.Job: Job job_1544126256129_0002 running in uber mode : false
18/12/08 18:30:56 INFO mapreduce.Job:  map 0% reduce 0%
18/12/08 18:31:34 INFO mapreduce.Job:  map 100% reduce 0%
18/12/08 18:31:53 INFO mapreduce.Job:  map 100% reduce 100%
18/12/08 18:31:54 INFO mapreduce.Job: Job job_1544126256129_0002 completed successfully
****计数器数据，可以不用关注
18/12/08 18:31:54 INFO mapreduce.Job: Counters: 49
	File System Counters
		FILE: Number of bytes read=44
		FILE: Number of bytes written=222903
		FILE: Number of read operations=0
		FILE: Number of large read operations=0
		FILE: Number of write operations=0
		HDFS: Number of bytes read=140
		HDFS: Number of bytes written=26
		HDFS: Number of read operations=6
		HDFS: Number of large read operations=0
		HDFS: Number of write operations=2
	Job Counters 
		Launched map tasks=1
		Launched reduce tasks=1
		Data-local map tasks=1
		Total time spent by all maps in occupied slots (ms)=34513
		Total time spent by all reduces in occupied slots (ms)=16266
		Total time spent by all map tasks (ms)=34513
		Total time spent by all reduce tasks (ms)=16266
		Total vcore-seconds taken by all map tasks=34513
		Total vcore-seconds taken by all reduce tasks=16266
		Total megabyte-seconds taken by all map tasks=35341312
		Total megabyte-seconds taken by all reduce tasks=16656384
	Map-Reduce Framework
		Map input records=2
		Map output records=5
		Map output bytes=54
		Map output materialized bytes=44
		Input split bytes=105
		Combine input records=5
		Combine output records=3
		Reduce input groups=3
		Reduce shuffle bytes=44
		Reduce input records=3
		Reduce output records=3
		Spilled Records=6
		Shuffled Maps =1
		Failed Shuffles=0
		Merged Map outputs=1
		GC time elapsed (ms)=385
		CPU time spent (ms)=3340
		Physical memory (bytes) snapshot=354152448
		Virtual memory (bytes) snapshot=5517279232
		Total committed heap usage (bytes)=208142336
	Shuffle Errors
		BAD_ID=0
		CONNECTION=0
		IO_ERROR=0
		WRONG_LENGTH=0
		WRONG_MAP=0
		WRONG_REDUCE=0
	File Input Format Counters 
		Bytes Read=35
	File Output Format Counters 
		Bytes Written=26

```
- 任务执行的过程，我们在yarn上也是可以实时查看到情况的
![](https://upload-images.jianshu.io/upload_images/2572206-52fd03efa0e1fdaf.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
