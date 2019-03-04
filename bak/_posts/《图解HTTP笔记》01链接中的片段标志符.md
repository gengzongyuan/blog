title: 《图解HTTP笔记》01链接中的片段标志符
date: 2018-09-27 15:04:26
tags: [HTTP,网络协议]
categories: [开发]
---
看下面这段链接，常规的链接内容我们都清楚，但是#后面的内容又是什么呢？
![](https://upload-images.jianshu.io/upload_images/2572206-40e69c45969a5b22.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

**01定位（锚点） -- 跳转到页面指定位置**
```
创建html文件
<html>
	<head>
		<script src="jquery/jquery.js"></script>
		<script type="text/javascript">
			$(document).ready(function(){
				// 在这里写你的代码...
				for(var i=0;i<10;i++){
				$("ol").append("<li>大树<li>")					
				}
			});
		</script>
	</head>
	<body>
		<ol></ol>
		<button id="btn1">id="btn1"</button>
		<a name="a1">name:a1</a>
		<ol></ol>
		<button name="btn2">name="btn2"</button>
		<a name="a2">name:a2</a>
		<ol></ol>
	</body>
</html>
```
```
分别访问
http://127.0.0.1:8848/w3schoolJquery1/nihao.html#a1
http://127.0.0.1:8848/w3schoolJquery1/nihao.html#a2
http://127.0.0.1:8848/w3schoolJquery1/nihao.html#btn1
http://127.0.0.1:8848/w3schoolJquery1/nihao.html#btn2
```
> 浏览器加载页面时，如果附带#string参数，将会对页面所有标签的id进行搜索，当id=string时，跳转到string对应的位置**（也可以使用name进行指定，但name仅使用于<a><a/>）**

**02 -- 会传递参数给服务器吗**
![](https://upload-images.jianshu.io/upload_images/2572206-527ff4b7eb668ac8.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

> 浏览器访问并进行抓包后发现，并不会将#后的参数传递给服务器

**03 -- 如果浏览器重新访问含#string的链接会向服务器发送请求吗？**
![浏览器访问](https://upload-images.jianshu.io/upload_images/2572206-c7ef39e192effee2.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
![抓包结果](https://upload-images.jianshu.io/upload_images/2572206-10a4886b2e670a25.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

> 在浏览器访问了三条记录，发现仅请求了服务器两次
**答案：不会**
