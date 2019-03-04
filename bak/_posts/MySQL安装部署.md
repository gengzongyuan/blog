title: MySQL安装部署
date: 2019-02-13T07:21:25.917Z
tags: [mysql]
categories: [运维]
---
#### MySQL安装卸载
##### RPM
```
- 安装
yum install -y mysql-server.x86_64
yum install -y mysql.x86_64
service mysqld start

mysql -uroot -p
Enter password: 直接回车
```
```
- 卸载
rpm -qa|grep mysql
mysql57-community-release-el6-11.noarch

rpm -e mysql57-community-release-el6-11.noarch
```

<!--more-->

#### 自主安装
- 下载并校验md5值
```
下载地址
链接: https://pan.baidu.com/s/1G9OPorT249LhwkZ9AHJShA 提取码: xx4p 复制这段内容后打开百度网盘手机App，操作更方便哦

cd /usr/local
rz

* md5sum校验文件md5看是不是有缺失
md5sum mysql-5.6.23-linux-glibc2.5-x86_64.tar.gz
61affe944eff55fcf51b31e67f25dc10  mysql-5.6.23-linux-glibc2.5-x86_64.tar.gz
```
- 检查是否安装过MySQL
```
ps -ef|grep mysqld
rpm -qa |grep -i mysql
```
- 解压移动
```
tar zvfx mysql-5.6.23-linux-glibc2.5-x86_64.tar.gz 
mv mysql-5.6.23-linux-glibc2.5-x86_64 mysql
```
- 创建dba用户组和mysqladmin用户
```
生产：专门的软件会有专门的用户去部署 运行
oa系统  oaadmin
erp系统  erpadmin

# 创建组
groupadd -g 101 dba		
cat /etc/group | grep dba
dba:x:101:

# 创建用户
useradd -u 501 -g dba -G root -d /usr/local/mysql mysqladmin
useradd: warning: the home directory already exists.# 提示用户家目录已经存在
Not copying any file from skel directory into it.	# 没有cp skel到家目录中
[root@hadoop614 etc]# id mysqladmin
uid=501(mysqladmin) gid=101(dba) groups=101(dba),0(root)

-------/etc/skel文件夹中存放了基础的用户配置，我们可以直接拿过来使用
cp /etc/skel/.* /usr/local/mysql
cp: omitting directory ‘.’
cp: omitting directory ‘..’
-------
```
- 创建 /etc/my.cnf  (640权限)
```
寻找顺序：/etc/my.cnf-->/etc/mysql/my.cnf-->SYSCONFDIR/my.cnf-->$MYSQL_HOME/my.cnf-> --defaults-extra-file->~/my.cnf

cd /etc/
vi my.cnf

[client]
port            = 3306
socket          = /usr/local/mysql/data/mysql.sock

[mysqld]
port            = 3306
socket          = /usr/local/mysql/data/mysql.sock

skip-external-locking
key_buffer_size = 256M
sort_buffer_size = 2M
read_buffer_size = 2M
read_rnd_buffer_size = 4M
query_cache_size= 32M
max_allowed_packet = 16M
myisam_sort_buffer_size=128M
tmp_table_size=32M

table_open_cache = 512
thread_cache_size = 8
wait_timeout = 86400
interactive_timeout = 86400
max_connections = 600

# Try number of CPU's*2 for thread_concurrency
thread_concurrency = 32

#isolation level and default engine 
default-storage-engine = INNODB
transaction-isolation = READ-COMMITTED

server-id  = 1
basedir     = /usr/local/mysql
datadir     = /usr/local/mysql/data
pid-file     = /usr/local/mysql/data/hostname.pid

#open performance schema
log-warnings
sysdate-is-now

binlog_format = MIXED
log_bin_trust_function_creators=1
log-error  = /usr/local/mysql/data/hostname.err
log-bin=/usr/local/mysql/arch/mysql-bin
#other logs
#general_log =1
#general_log_file  = /usr/local/mysql/data/general_log.err
#slow_query_log=1
#slow_query_log_file=/usr/local/mysql/data/slow_log.err

#for replication slave
#log-slave-updates 
#sync_binlog = 1

#for innodb options 
innodb_data_home_dir = /usr/local/mysql/data/
innodb_data_file_path = ibdata1:500M:autoextend
innodb_log_group_home_dir = /usr/local/mysql/arch
innodb_log_files_in_group = 2
innodb_log_file_size = 200M

#生产上 机械硬盘 sata盘 5000r 7200 10000 15000 ==> ssd 生产
# innodb_buffer_pool_size 调大 8G

innodb_buffer_pool_size = 1024M 
innodb_additional_mem_pool_size = 50M
innodb_log_buffer_size = 16M

innodb_lock_wait_timeout = 100
#innodb_thread_concurrency = 0
innodb_flush_log_at_trx_commit = 1
innodb_locks_unsafe_for_binlog=1

#innodb io features: add for mysql5.5.8
performance_schema
innodb_read_io_threads=4
innodb-write-io-threads=4
innodb-io-capacity=200
#purge threads change default(0) to 1 for purge
innodb_purge_threads=1
innodb_use_native_aio=on

#case-sensitive file names and separate tablespace
innodb_file_per_table = 1
lower_case_table_names=1

[mysqldump]
quick
max_allowed_packet = 16M

[mysql]
no-auto-rehash

[mysqlhotcopy]
interactive-timeout

[myisamchk]
key_buffer_size = 256M
sort_buffer_size = 256M
read_buffer = 2M
write_buffer = 2M
```
```
修改权限
chmod 640 my.cnf
chown mysqladmin:dba /etc/my.cnf
```
- 修改mysqladmin家目录权限
```
chmod -R 755 /usr/local/mysql
chown -R mysqladmin:dba /usr/local/mysql
```
-  切换mysqladmin用户创建arch目录  存储binlog  归档日志
```
su - mysqladmin
mkdir arch binlog
* 重新安装切记：rm -rf data rm -rf arch

scripts/mysql_install_db  \
> --user=mysqladmin \
> --basedir=/usr/local/mysql \
> --datadir=/usr/local/mysql/data 
```
```
mysql的数据转换到大数据中常用的方式
mysql-->maxwell-->kafka-->ss-->hbase
```
- 配置服务并开机自启动
```
exit
logout
# 将服务文件拷贝到init.d下，并重命名为mysql
cd /usr/local/mysql
cp support-files/mysql.server /etc/rc.d/init.d/mysql 
#赋予可执行权限
chmod +x /etc/rc.d/init.d/mysql
#删除服务
chkconfig --del mysql
#添加服务
chkconfig --level 345 mysql on
vi /etc/rc.local
在文件中追加：su - mysqladmin -c "/etc/init.d/mysql start --federated"
```
- 启动服务
```
方法1：
service mysql start
service mysql status
service mysql stop

方法2：mysqld_safe &
```

