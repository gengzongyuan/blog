title: python-01常用数据类型
date: 2018-09-27 14:56:58
tags: [python]
categories: [开发]
---
### 字符串

**字符串常用方法**

<!--more-->

```
name = "daaaaaashu"
#字符串首字母大写
print(name.capitalize())
# 把字符串进行字符补充，不够的位置补*
print(name.center(1,"*"))
# 统计字符出现的个数
print(name.count("s")) # 找不到时返回0
# 以XX结尾，True/False 上传文件时判断是否以什么结尾的时候用的到
print(name.endswith("shu"))
# 以XX开头 判断手机是否为138开头等需求的时候用的到
print(name.startswith("dashu"))
# 全部大写 
print(name.upper())
#全部小写
print(name.lower())
# 查找对应字符出现的下标
print(name.find('aasdasda')) # 找不到元素的时候会返回-1
print(name.index('a'))# 找不到的时候会报错
# 判断是否都为纯数字 True/False
print(name.isdigit())
# 判断是否是空格
print("   ".isspace())
# 只有英文或者数字时返回True
print("123asd".isalnum())
# 判断是否都是英文字符
print("qweA".isalpha())
# 去掉字符串两边的东西，默认是空格和换行,也可以指定对应的字符
print("     dasjiq          \n".strip())
```

**字符串格式化**
```
msg = '''
    %s你好：
        今天的日期是%s
'''%("耿宗源",str(datetime.time))
print(msg)
```

```
msg = "欢迎光临 {name}，今天的日期是{today}"
msg = msg.format(name="大树",today = datetime.datetime.today())
print(msg)
# 欢迎光临 大树，今天的日期是2018-07-01 10:04:55.084602
# 这种字符串格式化方式在参数比较多的时候效果显著，常用在sql语句中
```
```
# format_map可以直接把一个字典作为参数传入
d = {'name':'小黑','sex':"未知"}
words = '名字 {name},性别： {sex}'.format_map(d)
print(words)
```