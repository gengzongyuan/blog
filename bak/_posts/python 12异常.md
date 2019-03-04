title: python 12异常
date: 2018-09-27 15:02:47
tags: [python]
categories: [开发]
---
**常见异常处理**
```
# 字典key空报错
dic = {}
print(dic["dashu"])# KeyError: 'dashu'

# 数组越界报错
lis = [1,2,3]
print(lis[4]) # IndexError: list index out of range

# 类型转换报错
s = "sd1"
print(int(s)) # ValueError: invalid literal for int() with base 10: 'sd1'
```

<!--more-->

**try-except-finally**
```
# 当执行一个except后，不会执行之后的except
try:
    float("casdq")

    lis = [1,2]
    lis[3]

    dic = {}
    dic["123"]
except ValueError as e:
    print("ValueError",e.args)
except IndexError as e:
    print("IndexError",e.args)
except Exception as e:
    print("Exception",e.args)
finally :
    print("结束啦")
```
