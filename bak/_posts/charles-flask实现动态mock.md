title: charles+flask实现动态mock
date: 2018-09-20 17:22:46
tags: [mock,flask,charles]
categories: [测试]
---

#### charles+flask实现动态mock
##### 直入正题
工作遇到需要测试投放的广告效果，广告效果图是这样的
![广告图](https://upload-images.jianshu.io/upload_images/2572206-c6c40a778c7dead4.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/180)
想把广告图换成自己公司的图片上只要通过charles改改改就完了，但是怎么动态的改改改呢？


<!--more-->

##### 1.charles抓包设置
![步骤1](https://upload-images.jianshu.io/upload_images/2572206-681bfcda82a50c88.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


![步骤2](https://upload-images.jianshu.io/upload_images/2572206-7e17c0ea1ae03d00.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![步骤3](https://upload-images.jianshu.io/upload_images/2572206-e37bf2ab8e144211.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![步骤4：设置手机代理，ip是电脑ip](https://upload-images.jianshu.io/upload_images/2572206-7dda118ca22a1ae4.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

##### 2.设置map remote
- 找到关键请求
![找到关键请求](https://upload-images.jianshu.io/upload_images/2572206-4af4a69c2cc25290.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

- 打开Map Remote
![打开Map Remote](https://upload-images.jianshu.io/upload_images/2572206-dbc2dd7eef7a9307.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

- map remote配置
![map remote配置](https://upload-images.jianshu.io/upload_images/2572206-615bc20e080ea9d6.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


- 开启map remote
![开启map remote](https://upload-images.jianshu.io/upload_images/2572206-ee6ae000e6fb1982.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

- 实现思路
![](https://upload-images.jianshu.io/upload_images/2572206-086b478e533cb270.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
> 看到这很多同学可能就明白了，这不就是以前网络安全课或者在书上看到的黑客常用的攻击手段吗，没错，咱们今天体验一把

##### 3.书写中转器
- 安装flask
```
pip install flask
```
```
#-*-coding:UTF-8-*-
import flask
import requests
import json
```
- 替换方法
```
def replace_txt(json_data):
    data_id = ""
    for dic in json_data["data"]:
        data_id = dic

    list = json_data.get("data").get(data_id).get('list')
    for ad in list:
        print(ad)
        ad['txt'] = "网易新闻"
        ad['desc'] = "人体75%都是水，喝够水的人生到底有多赚？"
        ad['img']="http://cms-bucket.nosdn.127.net/fa04e6870fd647c8b49cdf4719247a6b20180531114309.jpeg?imageView&enlarge=1&thumbnail=1200y800&quality=85"
        ad['img2']="http://pgdt.gtimg.cn/gdt/0/9c59c1ca41aea62d77328526c4ef5eb9.PNG/0"
        ad['customized_invoke_url']="newsapp://doc/DJ4OSETE0511A0AV?spsAdDocId=DJ4OSETE0511A0AV&spsPrice=0&spsPicId=http%3A%2F%2Fcms-bucket.nosdn.127.net%2Ffa04e6870fd647c8b49cdf4719247a6b20180531114309.jpeg%3FimageView%26enlarge%3D1%26thumbnail%3D1200y800%26quality%3D85&spsAdPlanId=176&spsModelId=0&s=news_zhizi_android_01&spsAdId=2&spsExpandId=291&spsImpressionId=441203903548&spsextend=test2"
        if not ad.get('ext'):
            ad['ext'] = {}
        ad['ext']['appname']="网易新闻"
        ad['ext']['pkg_name']="com.netease.newsreader.activity"
        ad['ext']['appid']=100812722
        ad['pkg_download_schema']="taskApkId=0&packName=com.netease.newsreader.activity&taskAppId=&appId=&via=ANDROIDQQ.FEED.ADVERTISE.zvqj62cnik7wo01&versionCode=652&channel=000116083636353430393231;70225816"
        # ad['rl']="newsapp://doc/DJ4OSETE0511A0AV?spsAdDocId=DJ4OSETE0511A0AV&spsPrice=0&spsPicId=http%3A%2F%2Fcms-bucket.nosdn.127.net%2Ffa04e6870fd647c8b49cdf4719247a6b20180531114309.jpeg%3FimageView%26enlarge%3D1%26thumbnail%3D1200y800%26quality%3D85&spsAdPlanId=176&spsModelId=0&s=news_zhizi_android_01&spsAdId=2&spsExpandId=291&spsImpressionId=441203903548&spsextend=test2"
    # print(list)
    json_data['data'][data_id]["list"]=list
    return json_data
```
- 请求接收-处理-返回(处理部分：替换请求url，将返回的response替换成我们需要的response）
```
@server.route('/information',methods=["get","post"]) # server.route装饰器装饰方法
def information():
    url = flask.request.url
    # print("url:",url)
    url = url.replace("http://47.94.108.11:8999","http://info.gdt.qq.com")
    res = requests.get(url).json()
    if 'push' not in url:
        res = replace_txt(res)
    return json.dumps(res,ensure_ascii=False)
```
- 启动server
```
server.run(host='0.0.0.0',port=8999,debug=True) # server的启动
```
##### 4.看一下效果
![](https://upload-images.jianshu.io/upload_images/2572206-f74597ef83e8d247.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


![](https://upload-images.jianshu.io/upload_images/2572206-e312369279b95282.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/280)



