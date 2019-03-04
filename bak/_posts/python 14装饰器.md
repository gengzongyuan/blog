title: python 14装饰器
date: 2018-09-27 15:03:15
tags: [python]
categories: []
---
**不改变原来的代码，给函数添加新功能**

<!--more-->

demo1
```
import time

def timer(func):
    def war(*args, **kwargs):
        start_time = time.time()
        res = func(*args,**kwargs)
        end_time = time.time()
        timeNumber = float(end_time - start_time)
        print("%s函数运行的时间是%3f"%(func.__name__,timeNumber))
        return res
    return war

@timer
def sleep(number):
    number = int(number)
    time.sleep(number)
    return "我睡了%s秒"%number

sleep(2)
```

> 参考文章
http://python.jobbole.com/81683/