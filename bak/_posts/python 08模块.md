title: python 08模块
date: 2018-09-27 15:00:22
tags: [python]
categories: [开发]
---

#### 1、标准模块
**time模块 -- 时间处理**

<!--more-->

```
print(time.time()) #获取当前时间戳 1530435582.808
print(time.strftime("%Y-%m-%d-%H-%M-%S")) #获取当前的格式时间
print(time.gmtime()) # 获取时间元组，如果不传时间戳，取的是标准时区的时间元组
print(time.localtime())# 获取时间元组，如果不传时间戳，取的是当前时区的时间元组
print(time.strftime("%Y-%m-%d-%H-%M-%S",time.localtime(1530435582 - 86400))) #strftime 如果传递一个格式和时间戳，将会返回格式化时间

def timestampToStr(timestamp=None,format="%Y-%m-%d-%H-%M-%S"):
    if timestamp:
        mytime = time.localtime(timestamp)
        mytimeStr = time.strftime(format,mytime)
        return mytimeStr
    else:
        return time.strftime("%Y-%m-%d-%H-%M-%S")

def strTotimestamp(format_time=None,format="%Y-%m-%d-%H-%M-%S"):
    if format_time:
        res = time.strptime(format_time,format)
        return time.mktime(res)
    else:
        return time.strftime(format)
```
**datetime模块 -- 时间处理**
```
print(datetime.date.today()) # 2018-07-08 获取年月日格式化时间
print(datetime.datetime.today()) # 2018-07-08 11:27:27.897401 获取精确格式化时间
# 通过timedelta计算时间差
res = datetime.datetime.today() + datetime.timedelta(days=1,hours=24,minutes=20,seconds=20.2)
print(res.time()) # 14:16:23.456000
print(res.date()) # 2018-07-10
print(res.timestamp()) #时间戳 1531203383.456
print(res.strftime("%Y %m") # 2018 07
```

**os -- 操作系统**
```
# os模块--操作系统
print(os.getcwd()) # 获取当前路径
print(os.path.exists("spz")) # 判读目录是否存在
print(os.path.isfile()) # 判断是否为文件
print(os.path.isdir()) # 判断是否为文件夹
os.mkdir("dashu\\spz") #创建目录  如果目录已经存在会报错 如果父目录不存在的时候也会报错
os.makedirs("spz\\dashu\\xiaohua") # 如果父目录不存在的时候会自动创建
print(os.listdir("d:\\")) # 显示目录下的所有文件
print(os.path.dirname()) # 获取父目录的路径
print(os.path.getsize("模块.py")) # 获取文件大小
print(os.sep) # 获取系统的路径分割负号
print(os.path.join("log","log2","log3")) # 根据系统自动拼接
print(os.walk('log')) # 获取一个目录下的所有文件**包含子目录**

print(os.path.abspath("."))# 获取当前绝对路径
print(os.getcwd())
os.chdir("..\\day03") # 修改当前路径
print(os.getcwd())
res = os.system("ipconfig") # 执行cmd指令，返回0成功1失败
res2 = os.popen("ipconfig").read()# 执行cmd执行，并将执行结果返回

# 常见组合使用
isExists = os.path.exists("spz\\dashu\\xiaohua")
if not isExists:
    os.makedirs("spz\\dashu\\xiaohua")
else:
    print('文件夹已建立')
```
**random -- 随机模块**
```
print(random.random()) # 取小于1的随机小数
print(random.randint(1,10))# 指定范围，取[a,b]范围随机整数
nums = range(1,10)
print(random.choice(nums))# 随机选择一个
print(random.sample(nums,3))# 随机选择n个，当n超过nums数量时会报错
```
**sys -- 系统模块**
```
import sys
command = sys.argv
print(command) # ['D:/it/pythondemo/python/study/day07/sys模块学习.py']
当命令行执行
python sys模块学习.py helloworld # ['sys模块学习.py', 'helloworld']

# 将当前文件的双层父目录，添加进环境变量
base_path = os.path.dirname(os.path.dirname(os.path.abspath(__file__)))
sys.path.insert(0,base_path)
```


**标准模块实战 - 清理日志**
```
题目：
1、写一个程序，创建一些数据。
    1、创建10个文件夹，文件夹名字自定义
    2、每个文件下面有10个 日志文件，
            文件名是从今天开始的前10天
                android_2018-07-01.log
                android_2018-06-30.log
                android_2018-06-29.log
                android_2018-06-28.log
                android_2018-06-28.log
                android_2018-06-28.log
                android_2018-06-28.log
    3、随机选3个文件，往里面写点东西
2、写一个程序，把上面那些空文件删掉，还有3天前的删掉
```
```
# 答案 day05
https://git.coding.net/dashuG/my_python.git
```
#### 2、第三方模块
```
pip源：https://pypi.org/search/?q=
```
**安装**
```
1. pip 安装
pip install pymysql
2.下载whl文件后
pip install redis-2.10.6-py2.py3-none-any.whl
3.下载.tar.gz包解压进入目录后
python setup.py install
```
**nnlog -- 日志模块**
```
import nnlog

my_log = nnlog.Logger('dashu.log',when='D',backCount=5) # 日志名称 更新方式'D'时每天  backCount:最大保存数量
my_log.debug("debug level")
my_log.info("info level")
my_log.warning("waring level")
my_log.error("error level")
````

**redis -- 非关系型数据库**
```
# 连接
r = redis.Redis(host='118.24.3.40',port='6379',password='password',db=10)
```
```
# k,v类型
r.set('dashu_name','gengzongyuan',60)# k,v,失效时间
res = r.get('dashu_name1')
res = r.delete('dashu_name1')
print(r.keys('*info*'))
r.flushdb()  # 清空数据库
print(res.decode()) # 由于返回的数据为二进制类型，需要进行decode()
```
```
# 哈希类型
r.hset('session_abs','session_gzy3','dciOIUIBOIVJIORFVJO3')
print(r.hgetall('session_abs')) # 获取所有
print(r.hget('session_abs','session_gzy')) # 获取单个
r.hdel('session_abs','session_gzy') # 删除
r.expire('session_abs',5)# 设置失效时间
print(r.ttl('session_abs'))# 剩余失效时间
```
**hashlib -- 加密模块**
```
import hashlib

s = 'gengzongyuan'
print(s.encode())
m = hashlib.md5(s.encode())
print(m.hexdigest())
# md5不可逆，所有一样的字符串，md5之后的结果都是一样的，往上的md5破解是通过撞库实现的
```
**pymysql -- 数据库模块**
```
# 连接
ip = "XX.XX.XX.XX"
port = 3306# 一定要Int类型
username = "root"
password = "123456"

coon = pymysql.connect(
    host=ip,
    port=port,
    user=username,
    password=password,
)
```
```
cur = coon.cursor() #建立游标
# cur = conn.cursor(cursor=pymysql.cursors.DictCursor) 建立dict类型的游标
res = cur.execute('SHOW DATABASES;') # 仅仅执行sql语句，不会返回数据
res = cur.fetchall() # 获取游标执行结果
res = cur.fetchmany(2) # 获取游标执行结果
res = cur.fetchone() # 获取游标执行结果
print(res)
cur.close()
coon.close()
```
```
# 当有修改或新增动作的时候一定要记得commit
conn.commit()
```
```
# 使用discription提取表头
cur.execute('SELECT * FROM user LIMIT 0;')
fileds = [filed[0] for filed in cur.description]
print(fileds)
```
**pymongo -- mongoDB模块**
```
import pymongo

client = pymongo.MongoClient(host="123.207.154.169",port=27017)
db = client["spz"] # 选择数据库，如果数据库不存在，会创建
stu_info_db = db['stu_info'] # 选择库中的表

# 增
stu_info_db.insert({"username":"zms","age":11,"jicheng":[{"zhengshu":"PMP","date":"2011年"},{"证书":"信息系统项目管理师","date":"2012年"}],"addr":"江西"})
# 查
stu_info_db.find({"username":"zms"})
# 删
stu_info_db.delete_one({"username":"zms"}) # 当数据库中有多条记录时，删除找到的第一条记录
stu_info_db.delete_many({"username":"zms"})
# 改
stu_info_db.update({"username":"zms"},{"name":"update"})
```
**xlrd -- excel读取模块**
```
book = xlrd.open_workbook('excelData/user.xls') # 读取excel
sheet = book.sheet_by_index(0) # 根据下标选取sheet
# book.sheet_by_name('user') # 根据name选取sheet
print(book.nsheets)

print(sheet.cell(1,1).value)
print(sheet.row_values(1))

print(sheet.nrows) # excel中的总行数
for i in range(sheet.nrows):
    print(sheet.row_values(i))
print(sheet.ncols) # excel中的总列数
for i in range(sheet.ncols):
    print(sheet.col_values(i))
```
**xlwt -- excel写模块**
```
import xlwt

book = xlwt.Workbook()
sheet1 = book.add_sheet('计划')
sheet1.write(0,0,'学生编号')
sheet1.write(0,1,'学生姓名')
sheet1.write(0,2,'学生成绩')
book.save('stu.xls')
```
**xlutils-- excel文件修改模块**
```
import xlrd,xlutils
from xlutils import copy

book1 = xlrd.open_workbook('save.xls')
new_book = copy.copy(book1)
sheet = new_book.get_sheet(0)
sheet.write(1,3,"19")
new_book.save('save.xls')
```


#### 3、自写模块




