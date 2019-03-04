title: UI自动化08 pyse第三方模块
date: 2018-09-21 17:33:07
tags: [selenium,自动化测试]
categories: [测试]
---

##### pyse介绍
> 在selenium的基础上，有前辈封装了一套简单的pyse模块，主要进行了几部分的封装
- 在element_wait中封装了显式等待
- 在get_element中封装了常用的find_element
- ···
其他的方法自行翻源码吧


<!--more-->

---------- 
##### 源码解析
- element_wait
```
    def element_wait(self, css, secs=5):
        if "=>" not in css:
            raise NameError("Positioning syntax errors, lack of '=>'.")

        by = css.split("=>")[0]
        value = css.split("=>")[1]

        if by == "id":
            WebDriverWait(self.driver, secs, 1).until(EC.visibility_of_element_located((By.ID, value)))

******************************
传递两个参数,css和secs
css会进行split分割，以“=>”分割
左边的是判断条件，右边的是对应参数
secs是调用WebDriverWait的最长超时时间
**需要注意的是，检查时间已经写死默认1秒
**不会返回找到的内容，仅仅是做了一个等待操作
******************************
```
- get_element
```
    def get_element(self, css):
        if "=>" not in css:
            raise NameError("Positioning syntax errors, lack of '=>'.")

        by = css.split("=>")[0]
        value = css.split("=>")[1]

        if by == "id":
            element = self.driver.find_element_by_id(value)
        elif by == "name":
            element = self.driver.find_element_by_name(value)
******************
同样以=>进行分隔
分隔后根据前面的部分调用不同的方法
后面的作为参数进行传递
******************
```
- type-输入参数
```
    def type(self, css, text):
        self.element_wait(css)
        el = self.get_element(css)
        el.send_keys(text)
******************
输入的参数进行了element_wait校验
缺点是没法对一个element进行多次操作，只能进行css的重复复用
******************
```
- drag_and_drop
```
    def drag_and_drop(self, el_css, ta_css):
        self.element_wait(el_css)
        element = self.get_element(el_css)
        self.element_wait(ta_css)
        target = self.get_element(ta_css)
        ActionChains(driver).drag_and_drop(element, target).perform()
******************
底层封装了ActionChains的drag_and_drop操作，传入了两个element
******************
```
- click_text
```
    def click_text(self, text):
        self.driver.find_element_by_partial_link_text(text).click()
```
##### 实际使用
```
from pyse import Pyse
import time

# 打开浏览器
myp = Pyse("chrome")
# 设置屏幕大小为800*600
myp.set_window(800,600)
# 屏幕最大化
myp.max_window()
# 访问百度
myp.open("https://www.baidu.com")
# 通过“学术”链接进行访问
ele = myp.click_text("学术")
# 在查询框中输入大树
myp.type("css=>#kw","大树")
# 清空输入的内容
myp.clear("css=>#kw")
myp.type("css=>#kw","大树")
# 点击百度一下
myp.click("css=>#su")
# 鼠标悬浮在左侧的框里
myp.move_to_element("css=>.left_menu_logo")
# 点击右侧的登录
myp.click_text("登录")
# 输入用户名，密码
myp.type("css=>#TANGRAM__PSP_10__userName","18061751305")
myp.type("css=>#TANGRAM__PSP_10__password","XXXXX")
# 点击登录
ele = myp.get_element("css=>#TANGRAM__PSP_10__submit")
ele.click()
time.sleep(5)
# 关闭浏览器
myp.quit()
```
