title: python 03文件操作
date: 2018-09-27 14:58:33
tags: [python]
categories: [开发]
---
**文件打开操作**

<!--more-->

```
f = open('test.txt','w',encoding='utf-8') #读写默认是r
# w/w+的读写模式会清空文件，指针是在开头的
# r/r+文件指针在最前面
# a/a+文件指针在末尾
```
```
# 使用With打开多个文件，并且可以自动对文件进行关闭
with open("回顾.py",encoding='utf-8') as fw,open("回顾.py",encoding='utf-8') as fw2:
    f.read()
    f.write("大树")
```
**文件常用操作**
```
# 读
f.read()   # 将文件中的所有内容读取出来
f.readline()    # 读取文件中的一行
f.readlines()   # 读取文件里面所有的内容，返回的时一个list,每一行的内容放到一个list钟

# 常用操作 - 读取每一行的数据
for line in f.readlines():
    print line
```
```
# 写
f.write("大树\n")

list = ["大树1\n","大树2"]
f.writelines(list )  # 将list中的每一个参数写到文件中
# 等价于下面的代码
for s in list:
    f.write(i)

# 又是我们调用f.write()函数，再去文件查看可能并不会立刻查看到内容，这是由于文件中存在缓冲区，所以不是每次write只有都会写入到文件中
# 这时候可以使用fw.flush()函数把缓冲区的内容立即写入到磁盘中
f.flush()
```
```
# 清理
# 使用f.truncate()函数可以将整个文件清空，我们一般配合f.seek(0)将文件指针一定到文件开头，然后执行清空动作
f.truncate()
```
**文件指针**
**需要注意的是，进行文件操作的时候是存在文件指针的**

```
f = open('username.txt',"r",encoding='utf-8')
print(f.read()) # 此时文件指针已经移动到最后
print(f.read()) # 所以第二次第二次输出为空

f.seek(0) # 使用seek()函数将指针移动到开头
```

#### 进阶操作--文件修改的两种操作
```
# 文件修改
# 1、简单粗暴直接
#     1、先获取到文件里面所有的内容
#     2、修改内容
#     3、清空原来文件中的内容
#     4、写入修改的内容
#     5、数据量小的时候可以采用这种方式

f = open('file.txt','a+',encoding="utf-8")
f.seek(0)
all_data = f.read()
new_data = all_data.replace("你","you")
f.seek(0)
f.truncate()    #清空文件内容
f.write(new_data)
f.close
```
```
# 2、高效的处理方式
#     1、打开excel文件的时候会有一个隐藏文件，那个隐藏文件就是为了修改原文件了
#     2、先打开原来的文件，再打开一个空文件
#     3、循环处理原来文件里的每行数据，处理完后写入到新文件里
#     4、把原来的文件删除，把新文件的名字改为原来文件的名字
import os
# 把歌词前面的空格、去除空行、你换成you
with open('file.txt',encoding='utf-8') as fr,open(".file.txt",'w',encoding='utf-8') as fw:
    for line in fr:
        line = line.lstrip()
        if line:
            line = line.replace('你','you')
            fw.write(line)

os.remove('file.txt')
os.rename('.file.txt', 'file.txt')
```

#### 实战练习
```
# 对file.txt中的数据进行筛选，省略**以数字开头的数据并在末尾添加@163.com**
# file.txt文档
1GmSluQ
owhMb7k
QD8wStm
o1ASe8x
muPoYv9
7vUqAIX
26OEwAz
imSVgHR
p08TrGf
dIL9Xpy
JfM49gr
CXgny8Q
GaRPvXS
blNvmrp
zrID2Wh
zWmfP1c
bPw6yYU
PnDTMac
idlDgHC
lLO6Bev
egl7Hnx
G1bacTQ
xi7GOlt
fgeqYbP
WATwhPG
dbQM9uV
HJqZeh4
nU6Q3bk
et6Odij
xZ93fv7
6Xkn4iv
Fjhdszu
q4oZcWn
o58MZB6
1DvVOc8
2Inx0lc
XYROn5W
e7nKGgp
BcrGSJR
ET9uAdz
IxcSKZ8
3VcdoJ9
VBYNhKt
1DkAnaG
w6WarX1
82Vgt0m
eMrQhUA
uxNwTZm
Im2KefJ
aVkexK2
7LPOphv
eLF93DE
9VG7IXy
Xq5mURw
rO7R526
dcOt9uo
fMsaDyW
5epdhwA
ARKnFWc
Re3V9qd
hK9pXto
TWwzyta
c4umDOo
n27Khmz
GCQuW5w
KWPcNdC
xv9Tz2L
l6ou8y7
KItcVba
xqFVz9H
5CS1R0s
9XyaPQ0
uzsXafg
PY6Jf0l
1tsZ7Iy
zbEns8N
1sJR5Za
t1YmLI3
wi6La1S
N7J6kcO
jkFDV4r
8IERJS5
K3f4NxV
NWMZF7t
1ozmZjT
z3yW5Ra
4XRYauq
8l9UYOD
owgOvNI
ag2hEQ7
scgTC1z
uGArQgc
SyNpv8w
BnbmNDW
UQMZpj2
jpFBRNv
UsbfMjR
qaXhPy6
H2fj3LY
```
```
# 答案
import os

with open('email.txt',encoding='utf-8') as fr,open('.email.txt','w',encoding='utf-8') as fw:
    for line in fr:
        if not line[0].isdigit():
            line = line.strip() + "@163.com\n"
            fw.write(line)

os.remove("email.txt")
os.rename(".email.txt","email.txt")
```

----------




