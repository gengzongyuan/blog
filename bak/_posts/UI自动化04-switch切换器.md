title: UI自动化04 switch切换器
date: 2018-09-21 17:32:57
tags: [selenium,自动化测试]
categories: [测试]
---


#### switch切换器
切换器会在三种场景中使用到
- 切换浏览器tag
- 切换页面frame
- 切换alert
官方提供了两种switch给我们使用，一种已经过时了，我们先来看看最新的


<!--more-->

##### 1、SwitchTo类
```
from selenium.webdriver.remote.switch_to import SwitchTo
```
- alert切换器
```
# 确认
SwitchTo(driver).alert.accept()

# 取消
SwitchTo(driver).alert.dismiss()

# 如果alert上有文本框，可以通过send
SwitchTo(driver).alert.send_keys("输入内容")

# 返回alert上面的文本内容
print(SwitchTo(driver).alert.text)
```
- frame切换器
```
# 跳到指定frameName的frame中
# 当出现多层的时候，需要进行层级切换
SwitchTo(driver).frame("frameName")

# 跳到默认frame中（最外层frame中）
SwitchTo(driver).default_content()

# 跳到上层frame中
SwitchTo(driver).parent_frame()
```
- tag切换器
```
handles = driver.window_handles
 
# SwitchToWindows接受浏览器TAB的句柄
driver.switch_to.window(handles[1])
```

##### 2、已废弃的可以直接通过driver调用的switch_to方法
```
# 写一行代码，方便我们看底层源码
driver.switch_to

*********进入switch_to*********
    def switch_to(self):
        """
        :Returns:
            - SwitchTo: an object containing all options to switch focus into

        :Usage:
            element = driver.switch_to.active_element
            alert = driver.switch_to.alert
            driver.switch_to.default_content()
            driver.switch_to.frame('frame_name')
            driver.switch_to.frame(1)
            driver.switch_to.frame(driver.find_elements_by_tag_name("iframe")[0])
            driver.switch_to.parent_frame()
            driver.switch_to.window('main')
        """
        return self._switch_to


*********返回了self._switch_to，我们来看看_switch_to*********
        self._switch_to = SwitchTo(self)


*********相视一笑************
```
