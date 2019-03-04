title: python 05函数
date: 2018-09-27 14:59:14
tags: [python]
categories: [开发]
---
**函数的定义与调用**
```
# 函数就是一个功能，一个方法，简化代码
def say(name,sex="男"):  # 函数,必填参数,默认参数
    print("%s哈哈哈,性别：%s"%(name,sex))

say("大树","男")
say(sex="女",name="小花")  # 以函数参数的名称进行数据的传递
# 使用参数名调用函数时候需要注意，参数调用的部分需要放到函数的后面
```

<!--more-->

**可变参数**
```
# 可变参数   非必填，不限制参数个数
# 多余的参数放置到元组中
def send_mail(*email):
    print(email)

send_mail("dashu@163.com")
send_mail("dashu@163.com","dashu2@163.com","dashu3@163.com")
send_mail()

# ('dashu@163.com',)
# ('dashu@163.com', 'dashu2@163.com', 'dashu3@163.com')
# ()
```

```
# **传参
# 参数放置到了一个字典中
# 当传递的参数不是key-value的时候提示：TypeError: my1() missing 1 required positional argument: '***'
def my1(**kwargs):
    print(kwargs)

my1(name = "哈哈哈",sex = "男")
my1()
my1(type="car",a=1,c=1)
# {'name': '哈哈哈', 'sex': '男'}
# {}
# {'a': 1, 'c': 1, 'type': 'car'}

```


**函数的返回**
```
# 返回值如果需要用到函数的处理结果的话，那就写return，如果不要那就不用写
# 函数里面如果碰到return 函数立刻停止
def calc(a,b):
    return a*b
print(8000 + calc(5000,6))

# 返回多个结果
def dashu():
    name = "大树"
    sex = "男"
    age = 20
    return name,sex,age
print(dashu())
# ('大树', '男', 20)  当返回的数据过多时会以**元组**形式返回
```
**实战练习 - 小数判断**
```
题目：
输入一串字符，判断这个字符是不是一个小数（包含正小数和负小数）
```
```
def isLowNumber(number):
    # 1、判断小数点个数
    # 2、正小数：小数点左边和右边都是整数的时候才合法，按照小数点进行
    # 3、负小数：小数点左边必须以负号开头且只有一个负号，并且去掉负号之后是个数字
    number = str(number)
    if number.count('.') == 1:
        numberList = number.split('.')
        left = numberList[0]
        right = numberList[1]
        # print(numberList)
        if left.isdigit() and right.isdigit():
            return True
        if left.startswith('-') and left[1:].isdigit() and right.isdigit():
            return True
    return False
```

