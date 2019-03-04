title: UI自动化02 selenium安装与基础操作
date: 2018-09-21 17:32:53
tags: [selenium,自动化测试]
categories: [测试]
---


##### 1、安装
```
pip install selenium
```

<!--more-->

- 查看最新驱动版本号（笔者写文档的时候是2.41）`http://chromedriver.storage.googleapis.com/LATEST_RELEASE`
![](https://upload-images.jianshu.io/upload_images/2572206-16050bed9c6e2d8b.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

- 进入2.41`http://chromedriver.storage.googleapis.com/index.html`
![](https://upload-images.jianshu.io/upload_images/2572206-7dba0678af204051.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
- 查看notes，根据自己的Chrome下载对应版本，直接下载最新的也可以
![](https://upload-images.jianshu.io/upload_images/2572206-041011a784ed6404.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
![](https://upload-images.jianshu.io/upload_images/2572206-ec0b80a0b97e42ab.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
- 下载好之后将解压后的目录文件放到环境变量中.进入pycharm执行代码

```
from selenium import webdriver
driver = webdriver.Chrome()
driver.get('http://www.imdsx.cn)')
```

##### 基础操作
- 请求连接

```
driver.get("http://ui.imdsx.cn/uitester/")
```

- 执行js代码

```
js = "window.scrollTo(0,0);"
driver.execute_script(js)
```

- 操作浏览器

```
print(driver.get_window_size())  # 获取当前浏览器尺寸
driver.maximize_window()    # 最大化浏览器
driver.set_window_size(500,500) # 设置浏览器大小
```

- 截图操作

```
driver.get_screenshot_as_file("screenshot/test.png")
```

- 操作窗口tag

```
# ****  获取当前tag指针
print(driver.current_window_handle) # CDwindow-D32C65F58928B154475F88206D1F76E1

# ****  获取浏览器所有的tag指针
print(driver.window_handles)
```

- 关闭tag

```
driver.close()
```

- 关闭浏览器

```
driver.quit()
```


