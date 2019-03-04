title: UI自动化07 屏幕动作ActionChains
date: 2018-09-21 17:33:04
tags: [selenium,自动化测试]
categories: [测试]
---


- 点击操作`单击、双击、右键`
```
click_btn = driver.find_element_by_xpath('//input[@value="click me"]')  # 单击按钮
doubleclick_btn = driver.find_element_by_xpath('//input[@value="dbl click me"]')  # 双击按钮
rightclick_btn = driver.find_element_by_xpath('//input[@value="right click me"]')  # 右键单击按钮

ActionChains(driver).click(click_btn).double_click(doubleclick_btn).context_click(rightclick_btn).perform()  # 链式用法
```

<!--more-->

- 鼠标移动`移动到某个控件、移动到某个位置、移动到某个元素的某个位置`
```
write = driver.find_element_by_xpath('//input[@value="Write on hover"]')  # 鼠标移动到此元素，在下面的input框中会显示“Mouse moved”
blank = driver.find_element_by_xpath('//input[@value="Blank on hover"]')  # 鼠标移动到此元素，会清空下面input框中的内容
result = driver.find_element_by_name('t1')

action = ActionChains(driver)
action.move_to_element(write).perform()  # 移动到write，显示“Mouse moved”
# action.move_to_element(blank).perform()
action.move_by_offset(10, 50).perform()  # 移动到距离当前位置(10,50)的点，与上句效果相同，移动到blank上，清空
action.move_to_element_with_offset(blank, 10, -40).perform()  # 移动到距离blank元素(10,-40)的点，可移动到write上
```
- 拖拽`点击、移动、放松`
```
action.drag_and_drop(dragger, item1).perform()  # 1.移动dragger到目标1
action.click_and_hold(dragger).move_to_element(item1).release().perform()  # 3.效果与上两句相同，也能起到移动的效果
********解析**********
drag_and_drop底层其实是对click_and_hold、move_to_element和release三个方法的封装
```
- 键盘动作`按下、松开`
```
def key_down(self, value, element=None)
def key_up(self, value, element=None)
```

