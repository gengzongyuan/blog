title: python 02数据结构
date: 2018-09-27 14:58:16
tags: [python]
categories: [开发]
---
### list数据结构 - 有序的结构体

<!--more-->

```
# 增加
lis.append("小花")
lis.insert(0,"大白")
lis.append("小花")
# 删除
lis.pop(0)
lis.remove("小花") # remove的时候只remove找到的第一个参数
# 修改
lis[0] = "大叔"
# 查询
print(lis.index("小花"))
# 排序
lis.sort()
# 反转
lis.reverse()
#清除
lis.clear()
#查找个数
print(lis.count("大叔"))
```
### 元组 - 无法修改
```
# 元组具有无法修改的特点，我们使用元组的时候默认告诉对方，**这是一个无法修改的内容**
# 在一些数据库连接信息的定义时，我们可以采用元组
mysql_coon = ('192.168.1.109','root','123456',3306,'my_db')#定义元组
```

### 字典 - 独有的key-value数据结构
**字典常用操作**
```
# 字典的定义
map1 = {
	"name":"大树",
	"age":"20",
	"sex":"男",
	"qq":565506019
}

# 增操作
map1["marry"] = "未婚"
map1.setdefault("marry","未婚")

# 删除
map1.pop('marry')  #指定key删除 删除的时候key不存在是会报错的
del map1['age']  #用del来删，删除的时候key不存在是会报错的

# 改就很简单啦，直接赋值
map1["marry"] = "已婚"
```

```
# 字典的遍历操作
d = { 'a':1,'b':2 }
for k,v in d.items():
	print(k,v)         #可以
# 一般采用下面的方式进行字典遍历，能够提高遍历效率
for k in d:
	print(k, d.get(k))
```

**实战操作 - 监控日志**
```
# 题目
 1、监控日志
     1、如果一分钟内某个ip访问超过100次
     2、拿到ip，加入黑名单
178.210.90.90 - - [04/Jun/2017:03:44:13 +0800] "GET /wp-includes/logo_img.php HTTP/1.0" 302 161 "http://nnzhp.cn/wp-includes/logo_img.php" "Mozilla/5.0 (Windows; U; Windows NT 5.1; en-US) AppleWebKit/533.4 (KHTML, like Gecko) Chrome/5.0.375.99 Safari/533.4" "10.3.152.221"
178.210.90.90 - - [04/Jun/2017:03:44:13 +0800] "GET /blog HTTP/1.0" 301 233 "http://nnzhp.cn/wp-includes/logo_img.php" "Mozilla/5.0 (Windows; U; Windows NT 5.1; en-US) AppleWebKit/533.4 (KHTML, like Gecko) Chrome/5.0.375.99 Safari/533.4" "10.3.152.221"
178.210.90.90 - - [04/Jun/2017:03:44:15 +0800] "GET /blog/ HTTP/1.0" 200 38278 "http://nnzhp.cn/wp-includes/logo_img.php" "Mozilla/5.0 (Windows; U; Windows NT 5.1; en-US) AppleWebKit/533.4 (KHTML, like Gecko) Chrome/5.0.375.99 Safari/533.4" "10.3.152.221"
66.249.75.29 - - [04/Jun/2017:03:45:55 +0800] "GET /bbs/forum.php?mod=forumdisplay&fid=574&filter=hot HTTP/1.1" 200 17482 "-" "Mozilla/5.0 (compatible; Googlebot/2.1; +http://www.google.com/bot.html)" "-"
37.9.169.20 - - [04/Jun/2017:03:47:59 +0800] "GET /wp-admin/security.php HTTP/1.1" 302 161 "http://nnzhp.cn/wp-admin/security.php" "Mozilla/5.0 (Windows; U; Windows NT 5.1; en-US) AppleWebKit/533.4 (KHTML, like Gecko) Chrome/5.0.375.99 Safari/533.4" "-"
37.9.169.20 - - [04/Jun/2017:03:48:01 +0800] "GET /blog HTTP/1.1" 301 233 "http://nnzhp.cn/wp-admin/security.php" "Mozilla/5.0 (Windows; U; Windows NT 5.1; en-US) AppleWebKit/533.4 (KHTML, like Gecko) Chrome/5.0.375.99 Safari/533.4" "-"
37.9.169.20 - - [04/Jun/2017:03:48:02 +0800] "GET /blog/ HTTP/1.1" 200 38330 "http://nnzhp.cn/wp-admin/security.php" "Mozilla/5.0 (Windows; U; Windows NT 5.1; en-US) AppleWebKit/533.4 (KHTML, like Gecko) Chrome/5.0.375.99 Safari/533.4" "-"
37.9.169.20 - - [04/Jun/2017:03:48:21 +0800] "GET /wp-admin/security.php HTTP/1.1" 302 161 "http://nnzhp.cn/wp-admin/security.php" "Mozilla/5.0 (Windows; U; Windows NT 5.1; en-US) AppleWebKit/533.4 (KHTML, like Gecko) Chrome/5.0.375.99 Safari/533.4" "-"
37.9.169.20 - - [04/Jun/2017:03:48:21 +0800] "GET /blog HTTP/1.1" 301 233 "http://nnzhp.cn/wp-admin/security.php" "Mozilla/5.0 (Windows; U; Windows NT 5.1; en-US) AppleWebKit/533.4 (KHTML, like Gecko) Chrome/5.0.375.99 Safari/533.4" "-"
37.9.169.20 - - [04/Jun/2017:03:48:23 +0800] "GET /blog/ HTTP/1.1" 200 38330 "http://nnzhp.cn/wp-admin/security.php" "Mozilla/5.0 (Windows; U; Windows NT 5.1; en-US) AppleWebKit/533.4 (KHTML, like Gecko) Chrome/5.0.375.99 Safari/533.4" "-"
42.236.49.31 - - [04/Jun/2017:03:49:04 +0800] "GET /questions HTTP/1.1" 200 41977 "http://bbs.besttest.cn/questions" "Mozilla/5.0 (Windows NT 6.1; WOW64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/50.0.2661.102 Safari/537.36; 360Spider" "-"
66.249.75.28 - - [04/Jun/2017:03:49:42 +0800] "GET /bbs/forum.php?mod=forumdisplay&fid=473&filter=digest&digest=1 HTTP/1.1" 200 17242 "-" "Mozilla/5.0 (compatible; Googlebot/2.1; +http://www.google.com/bot.html)" "-"
123.125.71.60 - - [04/Jun/2017:03:52:50 +0800] "GET /robots.txt HTTP/1.1" 302 161 "-" "Mozilla/5.0 (compatible; Baiduspider/2.0; +http://www.baidu.com/search/spider.html)" "-"
```
```
# 答案
2、思路
    1、打开日志文件
    2、对每一行进行读取，并split,读取第一个元素
    3、找出所有的ip,统计次数
    4、判断每个ip次数>100，就发邮件
    5、记录文件指针，给下次读的时候用
    6、等待60秒（）

import time
point = 0
while True:
    with open("access.log",'r',encoding='utf-8') as logFile:
        logFile.seek(point)
        ip_info = {}  # 存放ip和ip出现的次数
        for line in logFile:
            ip = line.split()[0]
            if ip in ip_info:
                ip_info[ip] += 1
            else:
                ip_info[ip] = 1
        point = logFile.tell() #获取当前文件指针的位置
        # print(ip_info)
        for k in ip_info:
            if ip_info[k] > 100:
                print("该%s  IP在攻击你"%k)
        time.sleep(5)
```


### 集合 - 天生去重

**集合的特点**
```
#  天生去重
list = [1,2,2,2,1,1,2,3,4,2]
print(set(list))  # {1, 2, 3, 4}

# 关联操作
# 集合的关联操作可以方便的对两个集合进行统计
```

**集合的常用操作**
```
# 定义集合
set1 = set()

# 集合增操作
set1.add(222)
set1.update([2,7,8]) # 当需要增加的内容是一个list的时候，使用update

# 集合删操作
set1.remove(222) #删除不存在的内容时会报错
set1.discard(8) # 不会报错
set1.pop() # pop出最左边的内容
```
**集合的关联操作**
```
# 初始定义
list = [0,1,2,2,2,1,1,2,3,4,2]
list_2 = [2,3,4,5,6,3,1,2,3,2]
set1 = set(list)
set2 = set(list_2)
# 并集
print(set1 | set2)
print(set1.union(set2))
# 交集
print(set1 & set2)
print(set1.intersection(set2))
# 差集
print(set2 - set1)
print(set2.difference(set1))
# 对称差集
print(set1 ^ set2)
print(set1.symmetric_difference(set2))
```

**实战操作 - 1 密码实战**
```
# 输入一串密码，需要包含数字、特殊字符、大写字母、英文字母
---------示例---------
当输入为abc > no
abc#$%213AM > yes
```
```
# 答案

import string
pwd = input("请输入你的密码：")
# 密码中要包含大写，小写字母和数字和特殊字符
pwd_set = set(pwd)

print(pwd_set)
# print(string.digits) #所有的数字
a = pwd_set.intersection(set(string.digits))
# print(string.punctuation) #所有的特殊字符
b = pwd_set.intersection(set(string.punctuation))
# print(string.ascii_lowercase) #所有的小写字母
c = pwd_set.intersection(set(string.ascii_lowercase))
# print(string.ascii_uppercase)# 所有的大写字母
d = pwd_set.intersection(set(string.ascii_uppercase))

if  a and b and c and d:
    print("yes")
else:
    print("no")
```
**实战操作 - 2 生成双色球**
```
题目：
	# .中奖号码由6个红色球号码和1个蓝色球号码组成。 ...
	#random模块的用法
		#写一个函数，产生双色球号码,输入几就产生多少条，这些号码不能重复，要写到文件里面
		# 1个篮球         1-16
		# 6个红色球       1-33
```
