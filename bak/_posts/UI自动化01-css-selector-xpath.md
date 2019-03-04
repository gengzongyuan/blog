title: UI自动化01 css selector&xpath
date: 2018-09-21 17:32:50
tags: [selenium,自动化测试]
categories: [测试]
---

##### 练习软件安装
- 安装49版本的FireFox
- 链接：https://pan.baidu.com/s/1V3aEkyoR_gAhQVGCks2I6w 密码：lw5u
- 通过拓展程序安装Firebug和FirePath
![](https://upload-images.jianshu.io/upload_images/2572206-28176eb71410e120.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
- F12找到FirePath
![](https://upload-images.jianshu.io/upload_images/2572206-5c7d22311d108e2f.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

<!--more-->


##### 1.xpath
- 通过路径解析XML的一种格式
- 写xpath是在做一个缩减范围的过程

```
//*   # 定位当前html的所有元素
//input  # 由全部元素缩减到指定元素类型

# 标签+属性方式进行定位
//select[@name="city" and @multiple="multiple" and @size="4"]

# 取反定位
//div[@class='driver']/div[@class="inner"]/input[not(@placeholder="请通过ID定位元素")]

# 层级定位,角标中的值代表：获取body中所有div在第二个位置的标签
# 获取body下的第二个div
//body/div[2]
```


##### css selector
- 通过路径解析HTML的一种格式

```
#i1  #通过id进行定位
.classname  #通过class定位
input  #通过标签进行定位

# 属性的模糊匹配 开头^=
input[placeholder^="请通过ID定"]

# 属性的模糊匹配 结尾$=
input[placeholder$="过ID定位元素"]

# 属性的模糊匹配 任意
input[placeholder*="元素"]

# 属性的取反
div:not([id="wrapper"])
```

- 层级获取

```
# first-child
body>:first-child
# last-child
body>:last-child
# nth-child(x)
body>:nth-child(3)
```
