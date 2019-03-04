title: 接口自动化02 yaml
date: 2018-09-21 17:33:11
tags: [自动化测试,接口自动化]
categories: [测试]
---


yaml是一种标记文件，我们可以使用yaml高效的保存我们的测试用例

```
key : value
list : 
  - value1
  - value2
  - value3
```

<!--more-->

```
-
  url : http://118.24.3.40/api/user/login
  method : post
  detail : 登录接口
  data:
    username : niuhanyang
    passwd : aA123456
  check:
      - sign
      - userId

-
  url : http://118.24.3.40/api/user/all_stu
  method : get
  detail : 获取所有学生信息
  headers :
    Referer: http://api.nnzhp.cn/
  data:
  check:
    -
      stu_info
```
```
import yaml,json
all_case = json.dumps(yaml.load(open('login.yaml',encoding='utf-8')),ensure_ascii=False,indent=4)
print(all_case)
```
```
[
    {
        "method": "post",
        "url": "http://118.24.3.40/api/user/login",
        "detail": "登录接口",
        "data": {
            "passwd": "aA123456",
            "username": "niuhanyang"
        },
        "check": [
            "sign",
            "userId"
        ]
    },
    {
        "method": "get",
        "data": null,
        "url": "http://118.24.3.40/api/user/all_stu",
        "headers": {
            "Referer": "http://api.nnzhp.cn/"
        },
        "detail": "获取所有学生信息",
        "check": [
            "stu_info"
        ]
    }
]
```

