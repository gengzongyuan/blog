title: python 13面向对象
date: 2018-09-27 15:03:03
tags: [python]
categories: [开发]
---
**构造函数**
```
    def __init__(self):
        # 构造函数，实例在被创建的时候执行
        print("构造函数，类在实例化的时候，会自动执行它")
```

<!--more-->

**析构函数**
```
    def __del__(self):
        # 析构函数，实例被销毁的时候才会执行
        print("是什么时候执行我呢")
```
**私有**
```
# 一个函数名或者变量名前面加__，这个函数或者变量就是私有的
# 只能在类里面使用，使用起来比较安全
    def __cry(self):
        self.__name = "私有变量"
        print("哇哇哇")
```
**类方法**
```
# 使用    @classmethod对方法或者变量进行装饰，相当于static,直接通过类名就可以进行调用
# 它可以使用类变量，cls.xxx
# 实例也可以直接通过self.xx来使用类方法
# 实例方法中是不能调用类方法的
    @classmethod
    def eat(cls):
        print("吃饭")
```
**静态方法**
```
# 就是一个普通的函数，只是定义在类里面而已
# 没办法调用类中的变量
    @staticmethod
    def run():
        print("跑步")
```
**属性方法**
```
    # 属性方法：看起来像变量的一个函数
    # 属性方法是不能定义入参的
    @property
    def sex(self):
        return 'femal'

my = my()
m.sex # femal
```

