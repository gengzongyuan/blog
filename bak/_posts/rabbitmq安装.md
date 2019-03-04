title: rabbitmq安装
date: 2018-09-20 13:19:51
tags: [rabbitmq,python]
categories: [运维]
---

### 单节点安装
**erl安装**
```
# erlang安装
yum -y install make gcc gcc-c++ kernel-devel m4 ncurses-devel openssl-devel unixODBC unixODBC-devel httpd python-simplejson
wget http://erlang.org/download/otp_src_19.3.tar.gz
tar -zxf otp_src_19.3.tar.gz
./configure --prefix=/usr/local/erlang
make
make install
```
<!--more-->
```
# 配置环境变量
vim /etc/profile
export ERL_HOME=/usr/local/erlang
export PATH=$PATH:$ERL_HOME/bin
source /etc/profile
```
```
# 测试
erl
```
**rabbitmq安装**
```
# rabbitmq下载地址
https://www.rabbitmq.com/releases/rabbitmq-server/
```
```
wget https://www.rabbitmq.com/releases/rabbitmq-server/v2.7.0/rabbitmq-server-generic-unix-2.7.0.tar.gz
tar -zxf rabbitmq-server-generic-unix-2.7.0.tar.gz 

# 日志文件
mkdir -p /var/log/rabbitmq
# RabbitMQ 使用 Mnesia 数据库存储服务器信息，比如队列元数据、虚拟主机等。
mkdir -p /var/lib/rabbitmq/mnesia/rabbit
```
```
./sbin/rabbitmq-server
```
### 集群配置
确保两台机器都正确安装rabbitmq
```
# 主机名称修改
vim /etc/sysconfig/network
NETWORKING=yes
HOSTNAME=host1
# 也可以使用指令临时修改主机名
hostnamectl set-hostname 新主机名

vim /etc/sysconfig/network
NETWORKING=yes
HOSTNAME=host2
```
```
# 同步host
vim /etc/hosts
ip1 host1
ip2 host2
```
将host1的rabbitmq cookie配置给host2
```
# 同步cookie
vim /root/.erlang.cookie
SLTASOPTCNCVPPREUWOV

复制SLTASOPTCNCVPPREUWOV
进入host2
vim /root/.erlang.cookie
修改
```
```
# 启动host1
sh sbin/rabbitmq-server -detached

# 启动host2并关闭服务
sh sbin/rabbitmq-server -detached
sh sbin/rabbitmqctl stop_app
sh sbin/rabbitmqctl reset

# 将host2添加到host1的节点中
sh sbin/rabbitmqctl cluster rabbit@host1

# 启动节点
sh sbin/rabbitmqctl start_app
```
到这里就安装成功了，笔者在添加节点的时候存在报错
```
Clustering node rabbit@tencent2 with [rabbit@cui] ...
Error: {failed_to_cluster_with,[rabbit@cui],
                               "Mnesia could not connect to some nodes."}
```
对这个错误从两个方向进行排查
```
主机名是否正确配置-能ping通
erlang版本和rabbitmq版本是否一致
可以配合./rabbitmqctl -n rabbit@host1 status指令，在host2上执行看能否正确连接host1
```






**yum安装**
```
yum install erlang
wget http://www.rabbitmq.com/releases/rabbitmq-server/v3.5.0/rabbitmq-server-3.5.0-1.noarch.rpm
yum install rpm
rpm -ivh rabbitmq-server-3.5.0-1.noarch.rpm

# 开启管理员界面
rabbitmq-plugins enable rabbitmq_management
# 添加管理员账号
rabbitmqctl add_user rabbitadmin 123456
# 分配用户标签
rabbitmqctl set_user_tags rabbitadmin administrator
#创建和赋角色完成后查看并确认
rabbitmqctl list_users      

service rabbitmq-server   restart
```
```
访问http://XXX.XXX.XXX.XXX:15672/
rabbitadmin/123456
```


> 参考文章
https://www.imooc.com/article/42017
https://www.jianshu.com/p/434eb8bfaa5f
