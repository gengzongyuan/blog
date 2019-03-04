title: python 06变量
date: 2018-09-27 14:59:25
tags: [python]
categories: [开发]
---
```
# 局部变量
    # 在函数里面定义的变量都是局部变量，出了函数之后就不能使用了
# 全局变量
    # 在文件最上面定义的变量，就是全局变量
```

<!--more-->

**demo**
```
# 局部变量
    # 在函数里面定义的变量都是局部变量，出了函数之后就不能使用了
# 全局变量
    # 在文件最上面定义的变量，就是全局变量

name = "小黑"

def hhh():
    name = "小白"
    print(name)

def hhh2():
    # name = "小白"
    print(name)

def hhh3():
    global name
    name = "小红"
    print(name)

# 函数内存在跟全局变量一样名称的局部变量时，无法声明
# def hhh4(name):
#     global name
#     name = "小蓝"
#     print(name)

# 此时可以使用globals()[f'name']的方式获取全局变量
# 但是注意，globals()['name']并不会获取全局变量的引用
def hhh5(name):
    name = globals()['name']
    name = "小五"
    print(name)

hhh()
hhh2()
hhh3()
# hhh4("小兰")
hhh5("小花")
print(name)

# 小白
# 小黑
# 小红
# 小五
# 小红
```
**tips**
```
# 字典和list，这种可变变量，不需要global进行声明
d = {"name":"abc"}

def my():
    d["sex"] = 29
    print(d)

my()
print(d)
```
