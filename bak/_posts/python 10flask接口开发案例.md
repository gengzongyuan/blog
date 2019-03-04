title: python 10flask接口开发案例
date: 2018-09-27 15:01:22
tags: [python]
categories: [开发]
---
**目录**
```
- bin      # 存储启动文件
  - start.py
- conf    # 存储配置文件
  - setting.py
- lib       # 存储代码
  - interface.py
  - tool.py
- logs    # 日志
  - logs
```

<!--more-->

**-bin-start.py**
```
import sys
import os

base_path = os.path.dirname(os.path.dirname(os.path.abspath(__file__)))
sys.path.insert(0,base_path)

from lib.interface import server
from conf.setting import SERVER_PORT

server.run(port=8999,debug=True)

```
**-conf-setting.py**
```
# 数据库配置
MYSQL_INFO = {
    'host': 'XXXXXXXXXXX',
    'user': 'root',
    'password': 'XXXXXXXXX',
    'port': 3307,
    'db': 'shuiping',
    'charset': 'utf8',
    'autocommit': True
}

# 服务器配置
SERVER_PORT = 8999
SERVER_HOST = "0.0.0.0"

# redis配置
REDIS_INFO = {
    "host":"XXXXXXXXXXXX",
    "port":"XXXXXXX",
    "password":"XXXXXX",
    "db":10
}

# mongo配置
```
**-lib-interface.py**
```
import flask,json,time
from tools.tool import my_db
from tools.tool import my_md5
from tools.tool import my_redis_hash
from tools.tool import my_redis_expire
from tools.tool import isLowNumber

def isUser(username,pw):
    res = my_db("SELECT password from user WHERE username = '{username}'".format(username=username))
    if res[0]["password"] == my_md5(pw):
        return True
    else:
        return False

def getSession(username):
    timeTrip = str(int(time.time()))
    return my_md5(username+timeTrip)

server = flask.Flask(__name__)

@server.route('/login',methods=["get","post"])
def api_login():
    res = {"code":5000,"message":"请求失败，未知异常"}
    method = flask.request.method
    if method == "GET":
        res = {"code":5001,"message":"请使用POST请求进行登录"}
    elif method == "POST":
        if flask.request.headers.get("Content-Type") == "application/json":
            jsonData = flask.request.json
            username = jsonData.get("username")
            pw = jsonData.get("password")
        else:
            username = flask.request.values.get("username")
            pw = flask.request.values.get("password")
        if username and pw:
            if isUser(username,pw):
                session = getSession(username)
                my_redis_hash(username,"session",session)
                my_redis_expire(username,86400)
                res = {"code":2000,"message":"登录成功","session":session}
            else:
                res = {"code":5002,"message":"用户名或密码错误"}
        else:
            res = {"code":5001,"message":"用户名或密码为空"}
    return json.dumps(res,ensure_ascii=False)

@server.route("/pay",methods=["get","post"])
def api_pay():
    res = {"code":5000,"message":"支付失败，未知异常"}
    method = flask.request.method
    if method == "GET":
        res = {"code":5001,"message":"请使用POST请求进行登录"}
    elif method == "POST":
        if flask.request.headers.get("Content-Type") == "application/json":
            jsonData = flask.request.json
            username = jsonData.get("username")
            session = jsonData.get("session")
            money = jsonData.get("money")
        else:
            username = flask.request.values.get("username")
            session = flask.request.values.get("session")
            money = flask.request.values.get("money")

        if username and session and money and (str.isdigit(str(money)) or isLowNumber(money)):
            user_session = my_redis_hash(username,"session")
            if user_session and session == user_session:
                sql = r"SELECT id,money FROM salary WHERE username = '{username}'".format(username=username)
                res = my_db(sql)
                if len(res) != 0:
                    user_money = float(res[0]["money"])
                    if user_money > float(money):
                        # 支付
                        sql = r"UPDATE salary SET money = {money} WHERE id = {id}".format(money=user_money-float(money),id=res[0]["id"])
                        my_db(sql)
                        res = {"code":2000,"message":"支付成功"}
                    else:
                        res = {"code":5004,"message":"支付失败，余额不足"}
                else:
                    res = {"code":5003,"message":"支付失败，无此用户的工资单"}
            else:
                res = {"code":5005,"message":"支付失败，请先登录"}
        else:
            res= {"code":5002,"message":"请输入正确的参数"}

    return json.dumps(res,ensure_ascii=False)
```
**-lib-tool.py**
```
import pymysql,hashlib,redis,time
from conf.setting import MYSQL_INFO
from conf.setting import REDIS_INFO

def my_db(sql,type=None):
    coon = pymysql.connect(**MYSQL_INFO)
    if type=="list":
        cur = coon.cursor()
    else:
        cur = coon.cursor(cursor=pymysql.cursors.DictCursor)
    sql=sql.strip()
    cur.execute(sql)
    sql_start = sql[:6].lower()#取sql的开头,转成小写
    if sql_start.startswith('select') or sql_start.startswith('show'):
        data = cur.fetchall()
    else:
        data = 'ok'
    cur.close()
    coon.close()
    return data

def my_md5(s):
    m = hashlib.md5(s.encode())
    return m.hexdigest()

def my_redis_str(k,v=None,time=None):
    r = redis.Redis(**REDIS_INFO)
    if v:
        if time and time > 0:
            return r.set(k,v,int(time))
        return r.set(k,v)
    else:
        value = r.get(k)
        if value:
            return value.decode()
        else:
            return None

def my_redis_str_del(k):
    r = redis.Redis(**REDIS_INFO)
    return r.delete(k)

def my_redis_hash(k1, k2=None, v=None):
    r = redis.Redis(**REDIS_INFO)
    if k2:
        if v:
            return r.hset(k1,k2,v)
        else:
            value = r.hget(k1,k2)
            if value:
                return value.decode()
            else:
                return None
    else:
        dic = r.hgetall(k1)
        for k,v in dic.items():
            dic[k.decode()] = dic.pop(k).decode()
        return dic

def my_redis_hash_del(k1,k2=None):
    r = redis.Redis(**REDIS_INFO)
    if k2:
        r.hdel(k1,k2)
    else:
        r.hdel(k1)

def my_redis_expire(k1,time=None):
    r = redis.Redis(**REDIS_INFO)
    if time:
        if time > 0:
            return r.expire(k1,time)
        else:
            return -1
    else:
        return r.persist(k1)



if __name__ == '__main__':

    my_redis_str("name", "大树先生")
    print(my_redis_str("name"))
    print(my_redis_str("sex"))
    print(my_redis_str_del("name"))
    print(my_redis_str("name"))

    my_redis_hash(k1="gengzongyuan",k2="name",v="gengzongyuan")
    print(my_redis_hash("gengzongyuan"))
    print(my_redis_hash("gengzongyuan","name"))
    print(my_redis_hash("niuhanyang"))
    print(my_redis_hash("niuhanyang","name"))

    print(my_md5("123456"))

    sql = r"SELECT * FROM user;"
    print(my_db(sql))
    print(my_db(sql,"list"))

    sql = r"INSERT INTO user(username,password) VALUE('大树先生','123456')"
    print(my_db(sql))

    sql = r"SELECT * FROM user;"
    print(my_db(sql))

    sql = r"SELECT * FROM user WHERE username = '大树'"
    print(my_db(sql))

    timeTrip = time.time()
    print(timeTrip)

    my_redis_hash("zyl","name","zhaoyanling")
    print(my_redis_hash("zyl"))
    print(my_redis_expire("zyl",20))
    print(my_redis_expire("zyl"))


    print(my_redis_hash("dashu","sex"))

    sql = r"SELECT money FROM salary WHERE username = '{username}'".format(username="大树")
    res = my_db(sql)
    print(res)

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
