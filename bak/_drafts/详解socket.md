title: 详解socket
date: 2018-09-27 15:36:15
tags: []
categories: []
---
http://blog.p2hp.com/archives/3061

http://www.cnblogs.com/and/archive/2010/05/19/1739482.html

https://blog.csdn.net/w369033345/article/details/72779553

https://www.jianshu.com/p/377273a0267f
```
104 struct socket {

105 socket_state state;

106

107 kmemcheck_bitfield_begin(type);

108 short type;

109 kmemcheck_bitfield_end(type);

110

111 unsigned long flags;

112

113 struct socket_wq __rcu *wq;

114

115 struct file *file;

116 struct sock *sk;

117 const struct proto_ops *ops;

118 }



```