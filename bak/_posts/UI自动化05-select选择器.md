title: UI自动化05 select选择器
date: 2018-09-21 17:32:59
tags: [selenium,自动化测试]
categories: [测试]
---


```
from selenium.webdriver.support.select import Select
```

<!--more-->

```
# 通过select选项的索引来定位选择对应选项(从0开始计数)
Select(s).select_by_index(5)
 
# 通过选项的value属性值来定位
Select(s).select_by_value('2')
 
# 通过选项的文本内容来定位
Select(s).select_by_visible_text('牡丹江')
 
# 返回第一个选中的optionElement对象
Select(s).first_selected_option
 
# 返回所有选中的optionElement对象
Select(s).all_selected_options
 
# 取消所有选中的option
Select(s).deselect_all()
 
# 通过option的index来取消对应的option
Select(s).deselect_by_index(1)
 
# 通过value属性，来取消对应option
Select(s).deselect_by_value('')
 
# 通过option的文本内容，取消对应的option
Select(s).deselect_by_visible_text('')
```
