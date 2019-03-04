title: python 04json处理
date: 2018-09-27 14:58:50
tags: [python]
categories: [开发]
---
> python对json的处理主要依赖json模块
```
import json
```

<!--more-->

**json模块常用方法**
```
# json字符串转为字典类型
user_info='''
    {"nhy":"123456","dashu":"12345"}
'''
user_dic = json.loads(user_info)
print(type(user_dic),user_dic) # <class 'dict'> {'nhy': '123456', 'dashu': '12345'}

# 字典转换为字符串类型----单引号会自动转为双引号
stu_info = {'laowang':{'cars':['BMW','Ben-z'],"wife":"fanbingbing"}}
stu_json = json.dumps(stu_info)
print(stu_json) # {"laowang": {"wife": "fanbingbing", "cars": ["BMW", "Ben-z"]}}

# 直接将dic类型的数据按照json格式写入到文本内
with open("stuinfo.json",'w',encoding="utf-8") as fw:
    # json.dump(stu_info,fw)  # 不需要再调用.write
    json.dump(stu_info, fw,indent = 4) # 使用indent可以对写入时的json缩进进行整理，方便我们查看

--------stuinfo.json
{
    "laowang": {
        "wife": "fanbingbing",
        "cars": [
            "BMW",
            "Ben-z"
        ]
    }
}
--------

# 直接从文件里拿，不需要再读取文件
with open("stuinfo.json",'r',encoding="utf-8") as fr:
    print(json.load(fr)) 


```
**tips**
 ```
# 当dump的内容存在中文的时候
print json.dumps('大树')
"\u4e2d\u56fd"
 
输出的会是'大树' 中的ascii 字符码，而不是真正的中文。

print(json.dumps("大树",ensure_ascii=False))
"大树"
```
