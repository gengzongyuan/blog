title: linux基础攻略大全
date: 2019-01-24T12:57:20.122Z
tags: [linux]
categories: [运维]
---
```
用户名@机器名  所在目录
[root@hadoop001 ~]#

1.  pwd  查看当前光标所在路径

2.  用户目录
root  超级管理员  /root
jepson  普通用户的  /home/jepson
linux系统  目录开始多是以根目录/  开始

家目录：~

3.  cd 切换目录
cd /home/jepson

cd [空格 or ~]都可以切换到家目录
cd -  回退到上一次路径
cd ..  切换到上一层目录
cd ../../  切换到上2层目录

```
<!--more-->
```

4. 目录（路径）
绝对路径(以/开头)  /home/jepson/app
相对路径  app/hadoop

5.  clear 清空屏幕 or ctrl+L快捷键

6.  ls  # 查看目录
ls -l  ==> ll
ls -l -a  ==> ls -la  # 查看隐藏文件or文件夹
# 隐藏文件or文件夹：以.开头的文件夹或文件
ll -h  # 查看文件夹大小
** ll -rt  # 快速定位最新的文件：  （-r排序  -t按照时间排序）

7.  ip
ifconfig  # linux
ipconfig  # window


8.  mkdir
mkdir  app  # 创建app文件夹
mkdir -p  app/data1  # 创建嵌套文件夹
mkdir 4 5 6  # 创建并行目录

9.  mv  移动
mv  原路径文件夹/文件  目标目录/文件名

生产使用：
当文件已经被写着，移动日志文件然后修改名称
tips:
一个目录最好以/结尾

10.  cp 复制
cp  原路径文件夹/文件  目标目录/文件名

cp -r 原目录  目标目录

mv和cp的区别
1、一份，两份
2、都可以改名字
3、cp复制文件夹的时候需要-r

11.  ---help  查看帮助

12.  查看文件
cat  文件内容一下子全部显示
more  文件内容一页一页的往下翻  按空格键往下  但是无法回退
less  文件内容  往下往上都可以  按键盘的上下箭头
tail  实时查看
tail -f xxx.log 实时查看  
tail -F xxx.log 实时查看  F=f+retry  (如果当时没有这个文件，会在三秒后重试)

生产实践：
log4j写满日志之后在切日志的时候
程序-->  xxx.log
  mv  xxx.log  xxx.log1
  touch xxx.log

这种情况在mv之后tail -f是没办法实时查看的
细节文章查看https://mp.weixin.qq.com/s/ngaJs6cEVBOjemNw88wjOw


13.  重写和追加
echo "www.dashu.com" > dashu.log  重写
echo "www.dashu.com" >> dashu.log 追加

14.  echo  输出打印
echo "你好"

15. 
linux移动和复制谁快：移动快
windows移动和复制谁快：移动快

16.  别名
ls -l ==> ll
alias  命令名="命令"
alias tojmeter="cd /home/jmeter/apache-jmeter-4.0/bin"
配置的别名只在当前session生效，如果想长时间生效，配置到环境变量里

17.
当前session
永久生效配置：
  全局文件  /etc/profile  root  jepson等等等都可以去使用
  当前用户文件  ~/.bash_profile or ~/.bashrc

source  /etc/profile  # 配置环境变量生效
. .bash_profile也是可以的

18.touch  创建新文件

19.
rm dashu.log  会进行提示
rm -f dashu.log  强制删除，不会进行提示
rm -rf  logs/  删除文件夹

20.  环境变量
name='dashu'
echo ${name}

编写shell的时候需要注意，要考虑到shell中用到的参数会不会有为空的情况
name=''
rm -rf ${name}/data
这时候指令就变成了rm -rf /data
```
```
21.用户、用户组常用命令
[root@tencent1 ~]# ll /usr/sbin/user*
-rwxr-x---. 1 root root 111320 May 11  2016 /usr/sbin/useradd
-rwxr-x---. 1 root root  73656 May 11  2016 /usr/sbin/userdel
-rws--x--x  1 root root  42288 Aug 23  2010 /usr/sbin/userhelper
-rwxr-x---. 1 root root 115096 May 11  2016 /usr/sbin/usermod
-rwsr-xr-x  1 root root   9000 Jun 20  2018 /usr/sbin/usernetctl
[root@tencent1 ~]# ll /usr/sbin/group*
-rwxr-x---. 1 root root 59096 May 11  2016 /usr/sbin/groupadd
-rwxr-x---. 1 root root 54800 May 11  2016 /usr/sbin/groupdel
-rwxr-x---. 1 root root 54960 May 11  2016 /usr/sbin/groupmems
-rwxr-x---. 1 root root 73680 May 11  2016 /usr/sbin/groupmod


- 添加新用户
[root@tencent1 ~]# useradd dashu
[root@tencent1 ~]# id dashu
uid=503(dashu) gid=504(dashu) groups=504(dashu)
uid=用户id(用户名) gid=主组id(主组名) groups=组1id（组1名）,组2id（组2名）


配置：
cat /etc/passwd  # 用户信息
dashu:x:503:504::/home/dashu:/bin/bash
以：分隔，依次为
- 用户名-dashu
- 密码-x
- UID：用户id-503
- GID：主组id-504
- 注释性描述-
- 主目录-home/dashu
- 命令解释器-/bin/bash
https://blog.csdn.net/qq_34556414/article/details/78902662

cat /etc/group  # 组信息
dashu:x:504:
- 组名-dashu
- 口令：一般为空，可用来加密
- 组id-504
- 组内用户-只有一个时为空，多个时排列显示“，”分隔
https://blog.csdn.net/bbaibb1009/article/details/52667310

userdel dashu  # 用户删除
此时在看一下
/etc/passwd
/etc/group
可以看到记录已经被删除了
ll /home
可以看到dashu的家目录还在
- 当组内只有一个用户时，用户被删除的同时，组也会被删除
- 当用户被删除的时候，并不会清理家目录
此时我们再创建一下dashu用户，可以看到进行了对应的提示
[root@tencent1 ~]# useradd dashu
useradd: warning: the home directory already exists.
Not copying any file from skel directory into it.
Creating mailbox file: File exists

- 用户组操作
groupadd bigdata  # 创建一个bigdata组
usermod -a -G bigdata dashu  # 给dashu用户添加一个组bigdata
-a  append操作
-G  添加组信息
[root@tencent1 ~]# id dashu
uid=503(dashu) gid=504(dashu) groups=504(dashu),505(bigdata)
可以看到已经添加了对应的信息

usermod -g bigdata dashu  # 修改dashu的主组为bigdata
-g  添加主组信息
[root@tencent1 ~]# id dashu
uid=503(dashu) gid=505(bigdata) groups=505(bigdata)
使用-g的同时，其他组信息进行了清除操作
```
```
22. 操作历史 .bash_history文件
cat ~/.bash_history文件可以看到我们最近的指令
可以用来干擦屁股，哈哈哈
https://gengzongyuan.github.io/2019/01/22/%E5%88%A0%E5%BA%93%E8%B7%91%E8%B7%AF%E4%B9%8B%E7%9C%9F%E9%A6%99.bash_history/

23.  设置密码  passwd
passwd dashu
输入密码即可

24.  切换用户  su
su - ruoze
su ruoze
- 代表切用户后，进该用户的家目录且执行环境变量文件

25.  临时获取root最大权限sudo
vi /etc/sudoers修改文件
92 root    ALL=(ALL)       ALL
93 dashu ALL=(root) NOPASSWD:ALL
dashu用户可以在不输入自己密码的情况下，以root用户的身份，执行所有指令

https://blog.csdn.net/u011641885/article/details/47722699
授权用户 主机=[(可以切换到的用户，ALL代表所有，不写默认为root)][NOPASSWD: 不需要输入密码，不写默认需要输入密码][命令，ALL代表所有]
dashu ALL=(mysqladmin)  /bin/chown,/bin/chmod
dashu需要在输入自己密码的情况下，以mysqladmin的身份执行chown和chmod指令


26.  管道符 过滤  |
cat /etc/passwd | grep dashu
-v 过滤

27.  退出用户exit
su - dashu
exit

28.进程
ps -ef 

[root@tencent1 ~]# ps -ef|grep grep
root     20258 19978  0 20:45 pts/0    00:00:00 grep grep
用户  pid  父pid  CPU使用率  系统启动时间  登录者的终端机位置  使用CPU的时间
下达的指令

生产：机器夯住了/杀死进程  -->  杀死
kill  -9 pid
kill -9 pid1 pid2  # 并行杀
kill -9 $(pgrep -f log)  # 杀死所有包含log的进程
提醒：生产上不要随便杀进程,rm kill是高危命令，需要确认再确认


29.  端口号
netstat -nlp | grep pid  # 查询进程占用的端口
netstat -nlp |grep ssh # 通过名称查找
netstat -nlp | grep port  # 查看端口是否被占用
[root@tencent1 ~]# netstat -nlp|grep 8888
tcp        0      0 :::8888   :::*  LISTEN  1566/node

服务：ip：端口号
0.0.0.0:22
:::22
都表示了一个意思，代表当前ip对外提供访问
127.0.0.1:80  只针对当前机器内部访问

当出现Connection refused：错误时：
- ping ip
- tenlnet 端口

30.搜索 find
find ./ -name 'XXX'  # 从当前目录开始搜索
find / -name 'XXX'  # 从根目录开始搜索
* 代表模糊匹配

31. linux安装软件 rpm
yum search http
yum install -y httpd
yum remove httpd

使用rpm指令进行删除操作
[root@tencent1 ~]# rpm -qa|grep httpd
httpd-2.2.15-69.el6.centos.x86_64
httpd-tools-2.2.15-69.el6.centos.x86_64

rpm -e --nodeps httpd-2.2.15-69.el6.centos.x86_64
-e  删除
--nodeps  不关注依赖

32. which
which都是从$PATH中查找的
echo $PATH查看当前PATH

33.man 更加详细的帮助文档
yum --help可以看到是找不到remove指令的
man yum可以看到
        * remove | erase package1 [package2] [...]
```
```
34. vi操作
vi xxx.log文件
三模式：命令模式、尾行模式、编辑模式
常用快捷键：
dd  删除当前行
dG  删除光标以下的所有行
ndd  删除光标一下的n行，3dd 会删除当前行，共删除4行
gg  跳转至第一行的第一个字母
G  跳转到最后一行的第一个字母
shift+$  行尾
/dashu  从当前行开始向下查找dashu，按“n”键查找下一个
?dashu  从当前行开始向上查找dashu，按“#”键查找下一个
set nu  显示行号
set nonu  不显示行号

追加三连：G shift+$ i

生产注意事项：
修改conf文件时，一定记得先备份一份
cp xxx.conf xxx.conf20190124

35. 清空文件的坑
[root@tencent1 linuxtest]# ll
total 8
-rw-r--r-- 1 root root 43 Jan 24 19:55 vitest.log
-rw-r--r-- 1 root root 43 Jan 24 19:55 vitest.log2
[root@tencent1 linuxtest]# echo ''>vitest.log
[root@tencent1 linuxtest]# cat /dev/null >vitest.log2
[root@tencent1 linuxtest]# ll
total 4
-rw-r--r-- 1 root root 1 Jan 24 19:55 vitest.log
-rw-r--r-- 1 root root 0 Jan 24 19:55 vitest.log2

可以很明显的看到echo ''>xxx.log清空的文件大小是1字节的

36. 权限
-rw-r--r-- 1 root root 1 Jan 24 19:55 vitest.log
- rw- r-- r-- 1 root root 1 Jan 24 19:55 vitest.log

文件类型：d-文件夹 -代表文件 l代表软连接
rwx rwx rwx：共三组，分别代表 所属用户权限 用户组权限 其他用户权限
按照二进制排列，r-4 w-2 x-1 ,我们常说的755权限就是rwx r-x r-x
所属用户
所属用户组
文件大小
最后变更日期
文件名

37. 权限及用户组修改 chmod chown 
chmod 755 文件/目录
chmod -R 755 目录  -R代表给目录中所有的文件赋权

chown dashu:bigdata 目录/文件
chown -R dashu:bigdata 目录

38. 文件夹大小查看
du -sh /root

39. 硬连接 ln
ln -s linuxtest/ linuxtest-ln
drwxr-xr-x 2 root root   4096 Jan 24 19:55 linuxtest
lrwxrwxrwx 1 root root     10 Jan 24 20:08 linuxtest-ln -> linuxtest/
硬连接可以理解为一个快捷方式
cd linuxtest-ln可以正常的进入linuxtest文件夹
在删除linuxtest的时候，硬链接不会被删除

40. 上传 rz 下载 sz

41. 系统情况查看
- cpu
top
[root@tencent1 ~]# top
top - 20:13:37 up 167 days,  2:35,  2 users,  load average: 0.02, 0.02, 0.00
Tasks: 108 total,   1 running, 107 sleeping,   0 stopped,   0 zombie
Cpu(s): 29.4%us,  1.6%sy,  0.0%ni, 68.5%id,  0.1%wa,  0.0%hi,  0.4%si,  0.0%st
Mem:   1922148k total,  1763476k used,   158672k free,   315564k buffers
Swap:        0k total,        0k used,        0k free,   464756k cached
top常用快捷键
1：查看当前电脑每颗cpu的使用情况
shift + m：将进程按照内存排序

- 内存
free -m
[root@tencent1 ~]# free -m
             total       used       free     shared    buffers     cached
Mem:          1877       1722        154          0        308        453
-/+ buffers/cache:        960        916
Swap:            0          0          0

- 磁盘 df -h

42. 压缩解压
- 压缩
unzip -r a.zip 6/*
tar -czvf a.tar.gz 6/*

- 解压
tar -zxvf a.tar.gz
unzip a.zip

43. wget下载
wget 'http://www.ruozedata.com/'

44. 定时任务 crontab
crontab -e  编辑定时任务
*/1 * * * * echo '1' >> /root/linuxtest/crontabtest  
# 每1分钟往/root/linuxtest/crontabtest追加一个1
分 小时 天 月 周 *代表每的意思
*/1  每1
crontab -l  查看当前定时任务

思考题：我想每2秒往文件里写怎么办
[root@tencent1 ~]# cat test.sh 
for((i=1;i<=100;i++))
do
	date
	sleep 2
done
exit

* * * * * sh /root/test.sh >> /root/linuxtest/crontabtest

45. 后台操作
./test.sh &  控制台输出

nohup ./test.sh &  系统分配输出文件
[root@tencent1 ~]# nohup ./test.sh &
[1] 19544
[root@tencent1 ~]# nohup: ignoring input and appending output to `nohup.out'

nohup ./test.sh > testsh.log 2>&1 &  自定义输出文件
```
