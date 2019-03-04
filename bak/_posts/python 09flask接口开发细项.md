title: python 09flask接口开发细项
date: 2018-09-27 15:01:07
tags: [python]
categories: [开发]
---
**1.当前文件路径的导入**
```
base_path = os.path.dirname(os.path.dirname(os.path.abspath(__file__)))
sys.path.insert(0,base_path)
```

<!--more-->


**2.配置库的导入**
```
# 数据库配置
MYSQL_INFO = {
    'host': 'XXX',
    'user': 'root',
    'password': 'XXX',
    'port': 3307,
    'db': 'shuiping',
    'charset': 'utf8',
    'autocommit': True
}
```
```
coon = pymysql.connect(**MYSQL_INFO)
```
**3.接口配置**
```
import flask

server = flask.Flask(__name__) # 实例化server对象
@server.route('/login',methods=["get","post"]) # server.route装饰器装饰方法
def api_login():
    return "my first api"

server.run(port=8999,debug=True) # server的启动
```
**4.请求信息的读取**
```
# 获取get的参数
username = flask.request.args.get("username")
pwd = flask.request.args.get("password")
# 获取post - kv的参数
username = flask.request.values.get("username")
pwd = flask.request.values.get("password")
# 获取post - json的参数
jsonData = flask.request.json
username = jsonData.get("username")
pwd = jsonData.get("password")
```
**5.请求内容的判断**
```
method = flask.request.method
if method == "GET":
elif method == "POST":
```