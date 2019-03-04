title: ajax+jquery
date: 2018-09-27 15:00:34
tags: [ajax,jquery]
categories: [开发]
---
**发送post请求**

<!--more-->

```
<script src="/static/js/jquery.js"></script>
    <script type="text/javascript">
        $.ajax({
                url: '/api/login/',
                type :'POST',
                dataType: 'JSON',
                content_type: 'application/json',
                //POST发送的数据
                data: {
                    'username': username,
                    'password':password,
                    'csrfmiddlewaretoken': '{{ csrf_token }}',
                },
                //调用成功执行的函数
                success : function (data) {
                    alert("请求成功"+data)
                },
                //调用执行后调用的函数
                complete: function(XMLHttpRequest, textStatus){
                    alert("调用结束")
                },
                //调用出错执行的函数
                error: function(data){W
                    alert("请求出错"+data)
                }
        })
    </script>
```
**发送get请求**
```
$.get("/api/userInfo/",function(data,status){
    for(var i=0;i<data.users.length;i++){
        var user = data.users[i];
        $("ol").prepend("<li>"+user.username+user.password+"</li>");
    }
});
```
