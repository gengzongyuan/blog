title: Nginx负载调优总结
date: 2018-09-20 12:58:07
tags: [nginx,调优,性能测试]
categories: [测试]
---

### nginx负载调优总结


#### 一、调优手册
<!--more-->

|调优方向|需求指标|指标情况|
|:-|:-|:-|
|1. worker进程数是否合理|worker_processes auto;|nginx worker进程数量，建议各cpu核数相当|
|2. worker连接数是否合理|worker_connections 1024;|单个worker进程可服务的客户端数量（根据并发要求进行更改）|
|3.worker可打开最大文件数是否合理|worker_rlimit_nofile<br>ulimit -n|Nginx 最大可用文件描述符数量<br>linux可同时打开最大文件数|
|4. multi_accept|# multi_accept on|是否尽可能的接受请求（建议打开）|
|5. 读写方式是否合理|    sendfile            on;|直接从磁盘上读取数据到操作系统缓冲（建议打开）|
|6. tcp_nopush|    tcp_nopush          on;|在一个包中发送全部头文件（建议打开）|
|7. tcp_nodelay|    tcp_nodelay         on;|配置 nginx 不缓存数据，快速发送小数据|
|8.客户端在keep-alive的请求数量是否合理|keepalive_requests 100000;|建议配置较大的值|
|9.客户端通信超时时间是否合理|    keepalive_timeout   65;|超时时间|
|10.后端服务器超时时间是否合理|proxy_connect_timeout <br>proxy_read_timeout<br>proxy_send_timeout|默认60s|
|11.reset_timedout_connection配置是否合理|reset_timedout_connection on;|服务器在客户端停止发送应答之后关闭连接|
|12.request body读超时时间是否合理|client_body_timeout 10;|默认60s|
|13.types_hash_max_size|    types_hash_max_size 2048;|types_hash_max_size越小，消耗的内存就越小，但散列key的冲突率可能上升。|
|14.日志记录是否合理|access_log和error_log|access_log建议关闭，降低磁盘IO提高速度<br>error_log|
|15.压缩选用是否合理|gzip<br>gzip_disable<br>gzip_proxied<br>gzip_comp_level<br>gzip_types|设置数据压缩<br>设置禁止压缩<br>是否压缩基于请求、响应的压缩<br>压缩等级<br>压缩类型|
|16.linux系统是否启用epoll|use epoll;|Linux 关键配置，允许单个线程处理多个客户端请求。|
|17.缓存设置是否合理|open_file_cache max=200000 inactive=20s;<br>open_file_cache_valid 30s;<br> open_file_cache_min_uses 2;<br>open_file_cache_errors on;|缓存最大数目及超时时间<br>检测缓存源文件是否超时的间隔时间<br>缓存文件最小访问次数<br>缓存文件错误信息|

#### 二、参数获取方式及调优详解：
对nginx的调优很大一部分是对nginx配置文件的调优，配置文件我们可以在/etc/nginx/nginx.conf找到


##### 1.worker进程数是否合理
**指标获取**
```
...
worker_processes auto;
...
```
**指标分析**

worker_processes是worker进程的数量，默认值为auto，这个优化值受很多因素的影响，如果不确定的话，将其设置为**CPU内核数**是一个不错的选择

----------

##### 2.worker连接数是否合理
**指标获取**
```
...
worker_connections 1024;
...
```

**指标分析**

worker_connections 设置了一个worker进程可以同时打开的链接数，有高并发需求时，按照需求进行设定。链接**最大数目= worker_processes  * worker_connections**

----------

##### 3.worker可打开最大文件数是否合理
**指标获取**
```
...
worker_rlimit_nofile  200000; # 配置文件中默认不存在此项
...

[root@localhost nginx]# ulimit -n   #系统
1024     # 默认1024
```

**指标分析**

worker_rlimit_nofile为Nginx单个worker进程最大可用文件描述符数量，和链接数相同；**最大数目= worker_processes  * worker_rlimit_nofile**。
同时需要配置操作系统的 "ulimit -n XXXX"，或者在 /etc/security/limits.conf 中配置。 来达到对应配置。
配置数量按照需求情况设定，不建议配置较高的值。

----------

##### 4.multi_accept
**指标获取**
```
...
events {
    ...
     multi_accept on;     # 默认无此配置
}
...
```

**指标分析**
multi_accept 的作用是告诉 nginx 在收到新链接的请求通知时，尽可能接受链接。当然，得让他开着。

----------

##### 5.sendfile
**指标获取**
```
...
    sendfile            on;    #默认打开
...
```

**指标分析**
在 sendfile 出现之前，为了传输这样的数据，需要在用户空间上分配一块数据缓存，使用 read() 从源文件读取数据到缓存，然后使用 write() 将缓存写入到网络。
sendfile() 直接从磁盘上读取数据到操作系统缓冲。由于这个操作是在内核中完成的，sendfile() 比 read() 和 write() 联合使用要更加有效率。
**打开它**

----------

##### 6.tcp_nopush
**指标获取**
```
...
    tcp_nopush          on;默认打开
...
```

**指标分析**
配置 nginx 在一个包中发送全部的头文件，而不是一个一个发送。
这个选项使服务器在 sendfile 时可以提前准备 HTTP 首部，能够达到优化吞吐的效果。

----------

##### 7.tcp_nodelay
**指标获取**
```
...
tcp_nodelay on;
...
```

**指标分析**
不要缓存 data-sends （关闭 [Nagle 算法](http://blog.csdn.net/yuan1125/article/details/51536490)），这个能够提高高频发送小数据报文的实时性。**系统存在高频发送小数据报文的时候，打开它。**

----------

##### 8.keepalive_requests
**指标获取**
```
...
keepalive_requests  #默认为100
...
```

**指标分析**
设置通过"一个存活长连接"送达的最大请求数（默认是100，建议根据客户端在"keepalive"存活时间内的总请求数来设置）
当送达的请求数超过该值后，该连接就会被关闭。（通过设置为5，验证确实是这样）
**建议设置为一个较大的值**

----------

##### 9.客户端通信超时时间是否合理
**指标获取**
```
...
    keepalive_timeout   65;   #默认值65
...
```

**指标分析**
配置连接 keep-alive 超时时间，服务器将在超时之后关闭相应的连接。
指定了与客户端的 keep-alive 链接的超时时间。服务器会在这个时间后关闭链接。
keep-alive设置过小客户端和服务器会频繁建立连接；设置过大由于连接需要等待keep-alive才会关闭，所以会造成不必要的资源浪费。

----------


##### 10.后端服务器超时时间是否合理
**指标获取**
```
...
proxy_connect_timeout    #默认60
proxy_read_timeout    #默认60
proxy_send_timeout     #默认60
...
```

**指标分析**
- proxy_connect_timeout :后端服务器连接的超时时间_发起握手等候响应超时时间
- proxy_read_timeout:连接成功后_等候后端服务器响应时间_其实已经进入后端的排队之中等候处理（也可以说是后端服务器处理请求的时间）
- proxy_send_timeout :后端服务器数据回传时间_就是在规定时间之内后端服务器必须传完所有的数据


----------

##### 11. reset_timedout_connection配置是否合理
**指标获取**
```
...
reset_timedout_connection  off;#默认off
...
```


**指标分析**
允许server在client停止响应以后关闭连接,释放分配给该连接的内存。当有大并发需求时，建议打开。

----------

##### 12.request body读超时时间是否合理
**指标获取**
```
...
client_body_timeout   默认60s
...
```


**指标分析**
该指令设置请求体（request body）的读超时时间。仅当在一次readstep中，没有得到请求体，就会设为超时。超时后，nginx返回HTTP状态码408(“Request timed out”)

----------



##### 13.types_hash_max_size
**指标获取**
```
...
types_hash_max_size 1024; #默认1024
...
```

**指标分析**
types_hash_max_size影响散列表的冲突率。types_hash_max_size越大，就会消耗更多的内存，但散列key的冲突率会降低，检索速度就更快。types_hash_max_size越小，消耗的内存就越小，但散列key的冲突率可能上升。

----------

##### 14.日志记录是否合理
**指标获取**
```
...
access_log  /var/log/nginx/access.log  main;      #默认打开
error_log     #默认没此选项
...
```

**指标分析**
access_log确定了Nginx是否保存访问日志。将这个设置为关闭可以降低磁盘 IO 而提升速度。
error_log  设置Nginx记录临界错误，关闭同样可以提升IO速度；视情况而定，当确保Nginx不会出问题后，可以关闭，否则可以方面后期查看日志定位问题原因。**建议打开**
```
...
access_log  off;
error_log    /var/log/nginx/error.log;
...
```


----------

##### 15.压缩配置是否合理
**指标获取**
```
...
# nginx默认不进行压缩处理
    gzip on;
    gzip_disable "msie6";
    gzip_proxied any;
    gzip_comp_level 9;
    gzip_types text/plain text/css application/json application/x-javascript text/xml application/xml application/xml+rss text/javascript;
...
```

**指标分析**
- gzip  ：设置nginx gzip压缩发送的数据，**建议打开**
- gzip_disable：为指定的客户端禁用gzip功能，(IE5.5和IE6 SP1使用msie6参数来禁止gzip压缩 )指定哪些不需要gzip压缩的浏览器(将和User-Agents进行匹配),依赖于PCRE库
- gzip_proxied：Nginx作为反向代理的时候启用，根据某些请求和应答来决定是否在对代理请求的应答启用gzip压缩，是否压缩取决于请求头中的“Via”字段，指令中可以同时指定多个不同的参数，意义如下（**无特殊需求建议设置为any**）：
  - off(关闭所有代理结果的数据的压缩)
  - expired - 启用压缩，如果header头中包含 "Expires" 头信息
  - no-cache - 启用压缩，如果header头中包含 "Cache-Control:no-cache" 头信息
  - no-store - 启用压缩，如果header头中包含 "Cache-Control:no-store" 头信息
  - private - 启用压缩，如果header头中包含 "Cache-Control:private" 头信息
  - no_last_modified - 启用压缩,如果header头中不包含 "Last-Modified" 头信息
  - no_etag - 启用压缩 ,如果header头中不包含 "ETag" 头信息
  - auth - 启用压缩 , 如果header头中包含 "Authorization" 头信息
  - any - 无条件启用压缩
- gzip_comp_level：数据压缩的等级。等级可以是 1-9 的任意一个值，9 表示最慢但是最高比例的压缩
- gzip_types：设置进行 gzip 的类型；对于多数以文本为主的站点来说，文本自身内容占流量的绝大部分。虽然单个文本体积并不算大，但是如果数量众多的话，流量还是相当可观。启用GZIP以后，可以大幅度减少所需的流量。




----------

##### 16.linux系统是否启用epoll
**指标获取**
```
...
use epoll;  #默认无此配置
...
```


**指标分析**
Linux 关键配置，允许单个线程处理多个客户端请求。关于epoll，不做深入介绍，感兴趣的读者可以查看[nginx采用epoll的事件模型，为何效率高](http://zhan.renren.com/louxiaochang?gid=3602888498034749910&checked=true)

----------

##### 17.缓存设置是否合理
**指标获取**
```
...
open_file_cache off;   #默认off
open_file_cache_valid 60s;   #默认60s
open_file_cache_min_uses 1;   #默认1
open_file_cache_errors off;  #默认off
...
```

**指标分析**
open_file_cache：缓存最大数目及超时时间
open_file_cache_valid：检测缓存源文件是否超时的间隔时间
open_file_cache_min_uses：缓存文件最小访问次数
open_file_cache_errors on：缓存文件错误信息

建议按照实际情况对不同的配置组合进行尝试
```
    open_file_cache max=200000 inactive=20s;
    open_file_cache_valid 30s;
    open_file_cache_min_uses 2;
    open_file_cache_errors on;
```

#### 三、参考文档
[为最佳性能调优 Nginx](http://blog.jobbole.com/87531/)
[nginx优化 突破十万并发](http://www.cnblogs.com/yuanzai12345/p/5951860.html)
[Linux学习系列之Nginx调优实战](http://www.cnblogs.com/zhaijunming5/p/6034614.html)
[小灰灰分享：[3]Tsung压力/性能测试配置](https://jingyan.baidu.com/article/2009576183f908cb0721b405.html)
[详解Nginx中HTTP的keepalive相关配置](http://www.jb51.net/article/77595.htm)
[如何通俗地解释 CGI、FastCGI、php-fpm 之间的关系？](https://www.zhihu.com/question/30672017)
[](http://www.jb51.net/article/77253.htm)
[详解Nginx服务器中配置超时时间的方法]()
[Nginx基础入门之nginx.conf配置项相关介绍](http://blog.51cto.com/blief/1738388)
[Nginx配置优化说明](http://www.linuxidc.com/Linux/2013-07/86961.htm)
[nginx中的超时设置](http://blog.csdn.net/u014649204/article/details/39431773)
[nginx send_timeout 超时导致的302 错误](http://blog.csdn.net/pzqingchong/article/details/70146814)
[nginx配置长连接---keepalive相关](http://blog.csdn.net/senlin1202/article/details/54617635)
[nginx配置文件nginx.conf超详细讲解](http://www.cnblogs.com/liang-wei/p/5849771.html)
[使用Nginx缓存静态文件](http://blog.csdn.net/f529352479/article/details/68484280)
[nginx 的优化](https://www.shiyanlou.com/courses/95/labs/432/document)
[Nginx 配置优化选项](http://www.cnblogs.com/dongbatua/p/5202820.html)
[日志-nginx的access_log与error_log](http://blog.csdn.net/ty_hf/article/details/55518070)
[Nginx服务器中的GZip配置参数详解](http://www.jb51.net/article/71197.htm)
[nginx采用epoll的事件模型，为何效率高](http://zhan.renren.com/louxiaochang?gid=3602888498034749910&checked=true)



