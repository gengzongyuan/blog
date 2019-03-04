title: python 11网络编程request模块
date: 2018-09-27 15:02:08
tags: [python]
categories: [开发]
---
```
import request
```

<!--more-->

**get**
```
url2 = "http://118.24.3.40/api/user/login"
params= {"username":"niuhanyang","passwd":"aA123456"}
res = requests.get(url,params=params)
print(res.json())
```
**post -kv**
```
url2 = "http://118.24.3.40/api/user/login"
data = {"username":"niuhanyang","passwd":"aA123456"}
res = requests.post(url,data=data)
print(res.json())
```
**post -json**
```
url3 = "http://118.24.3.40/api/user/add_stu"
data = {
    "name":"大树先生",
    "grade":"天蝎座",
    "phone":"18061751234",
    "sex":"男",
    "age":29,
    "addr":"天安门"
}
res = requests.post(url3,json=data)
print(res.json())
```
**post -cookie**
```
url4 = "http://118.24.3.40/api/user/gold_add"
data = {'stu_id':15,'gold':200}
cookie = {'niuhanyang':'abd9a0995f4696e1a60133220b32037a'}
res = requests.post(url4,data=data,cookies=cookie)
print(res.json())
```
**post -header**
```
url4 = "http://118.24.3.40/api/user/gold_add"
data = {'stu_id':15,'gold':200}
headers = {'niuhanyang':'abd9a0995f4696e1a60133220b32037a'}
res = requests.post(url4,data=data,headers=headers)
print(res.json())
```
**post -下载**
```
requests.packages.urllib3.disable_warnings()
url7 = "https://aliimg.changba.com/cache/photo/855e5493-f018-44db-8892-c8660649327b_640_640.jpg"
res = requests.get(url7,verify=False)

with open('image.jpg','wb') as fw:
    fw.write(res.content)
```
**post -上传**
```
url7 = "http://118.24.3.40/api/file/upload"
data = {"file":open("image.jpg",'rb')}
res = requests.post(url7,files=data)
print(res.json())
```
**response**
```
print(res.text) # text信息，当返回内容为html页面时常用
print(res.content)# 二进制信息，当返回内容为文件或者图片时常用
print(res.json())# json信息
print(res.status_code)# http状态码
print(res.headers)# header
print(res.cookies)# cookie
```
**实战练习**
```
qq群成员头像爬虫
https://qun.qq.com/manage.html#click
- 根据qq群获取群内成员信息接口
https://qun.qq.com/cgi-bin/qun_mgr/search_group_members
data={
    'gc': "728755825",
    'st': 0,
    'end': 200,
    'sort': 0,
    'bkn': "130981834"
}
"cookie":"pt2gguin=o0565506019; RK=8NjM7jWBay; ptcz=ea9e485f239b5886a7e5f7014360962e52747c03ce0c49589752cf1f4ed5b9ef; _qpsvr_localtk=0.9268027128249254; pgv_pvi=2126511104; pgv_si=s3219082240; uin=o0565506019; skey=@5ZyOnqX0G; ptisp=cnc; p_uin=o0565506019; pt4_token=epS4vnP4YBkxZrMY9weuPCkx3zJKmn48pnXcCWvm3nI_; p_skey=3Bz1MjnnOg1*MfU4X4fILYOh11T*6qwl9MxmWba3-Dg_"

- 根据QQ号获取QQ头像接口
https://q4.qlogo.cn/g?b=qq&nk=565506019&s=140
```
```
import requests
import os

# 下载图片
def downloadImage(url,filepath,fileName):
    res = requests.get(url=url,verify=False)
    with open(os.path.join(filepath,fileName), 'wb') as fw:
        fw.write(res.content)

# 获取群内成员信息
url = "https://qun.qq.com/cgi-bin/qun_mgr/search_group_members"
data={
    'gc': "728755825",
    'st': 0,
    'end': 200,
    'sort': 0,
    'bkn': "130981834"
}
header = {"cookie":"pt2gguin=o0565506019; RK=8NjM7jWBay; ptcz=ea9e485f239b5886a7e5f7014360962e52747c03ce0c49589752cf1f4ed5b9ef; _qpsvr_localtk=0.9268027128249254; pgv_pvi=2126511104; pgv_si=s3219082240; uin=o0565506019; skey=@5ZyOnqX0G; ptisp=cnc; p_uin=o0565506019; pt4_token=epS4vnP4YBkxZrMY9weuPCkx3zJKmn48pnXcCWvm3nI_; p_skey=3Bz1MjnnOg1*MfU4X4fILYOh11T*6qwl9MxmWba3-Dg_"}
res = requests.post(url=url,data=data,headers=header,verify=False)
jsonData = res.json()
mems = jsonData["mems"]

# 根据返回信息提取昵称和qq
myUser = []
for memDict in mems:
    user = {}
    user["nick"] = memDict["nick"]
    user["uin"] = memDict["uin"]
    myUser.append(user)

# 下载图片
for user in myUser:
    qq = str(user["uin"])
    url = "https://q4.qlogo.cn/g?b=qq&nk=%s&s=140" % (qq)
    filepath = r"D:\it\pythondemo\my_python\day08\qq头像爬虫\image"
    fileName = qq + ".jpg"
    downloadImage(url, filepath, fileName)
```


