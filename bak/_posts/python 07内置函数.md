title: python 07内置函数
date: 2018-09-27 14:59:50
tags: [python]
categories: [开发]
---
```
import os

ranList = range(1,10)
print(ranList)
```

<!--more-->

**max**
```
# max() 取最大值 min() sum()
list1 = [1,2,32,23,43,12,53]
print(max(list1)) # 53
print(min(list1)) # 1
print(sum(list1)) # 166
```
**dir**
```
# dir 查看某个对象下的方法 .不出来方法的时候可以使用
message = "hello"
print(dir(message))
```
**sorted**
```
# sorted 排序操作
print(sorted(list1,reverse=True)) # [1, 2, 12, 23, 32, 43, 53]
print(sorted("dasdqkdjwdekq")) # ['a', 'd', 'd', 'd', 'd', 'e', 'j', 'k', 'k', 'q', 'q', 's', 'w']
# 也能对集合进行排序
set1 = set(list1)
print(sorted(set1)) # [1, 2, 12, 23, 32, 43, 53]
```
**chr**
```
# chr() 打印数字对应的ascil
# ord() 打印字符对应的ascil数字
print(chr(97))
print(ord('a'))
```
**round**
```
# round()  保留几位小数
print(round(2.999,2)) # 会自动四舍五入
```
**exec**
```
# exec() 执行python代码（定义一个函数也能执行）
# eval() 执行python代码（只能执行简单的 a = 1）
execdemo = '''
def my():
    print("大树先生的execdemo")

my()
'''
exec(execdemo) # 大树先生的execdemo

evaldemo = '''
1 + 1
'''
print(eval(evaldemo)) # 2
```
**enumerate**
```
# enumerate()枚举
stus = ["大一","大二","大三","大四"]
for i in range(len(stus)):
    print(i,stus[i])
for i,s in enumerate(stus):
    print(i,s)
for i,s in enumerate("大树先生"):
    print(i,s)
```
**zip**
```
# zip()将多个list压缩到一起,当多个list数目不一样时，取最少的
name = ["大树","小花","王五","张三"]
age = [21,23,32,36]
stus = ["大一","大二","大三","大四"]
for l in zip(name,age,stus):
    print(l)
# ('大树', 21, '大一')
#  ···
for name,age,stus in zip(name,age,stus):
    print(name,age,stus)

# print(map()) # 后续补充
# print(filter()) # 后续补充
```
**map**
```
# map生成函数生成器

DIRLIST = ["andriod", "iOS", "nginx", "tomcat", "mysql", "oracle", "loadrunner", "linux", "jmeter", "niujiaoshou"]

# 多个参数的时候没法使用，需要创建二维数组
res = (map(makdir,[str(i) for i in range(10000)]))
print(res)
for a in res:
    print(a)
```
```
# 生成器观察demo
# 通过实时观察计算机内存占用,可以看到，如果不通过生成器，需要将999999个数据先保存起来然后进行输出
import time

last_time = time.time()
nums = [str(i).zfill(10) for i in range(999999)]
for a in nums:
    print(a)
firtime = time.time() - last_time

print("-"*50)
time.sleep(3)

last_time = time.time()
nums = (str(i).zfill(10) for i in range(999999))
for a in nums:
    print(a)
sectime = time.time() - last_time

print(firtime,"---",sectime)
```
**filter**
```
# filter过滤器
def myFilter(num):
    if num%2 == 0:
        return True
# filter是过滤数据的函数，将过滤器返回结果为None或False的结果进行过滤
res = list(filter(myFilter,range(10)))
print(res)
```