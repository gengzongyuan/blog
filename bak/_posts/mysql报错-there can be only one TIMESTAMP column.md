title: mysql报错-there can be only one TIMESTAMP column
date: 2018-09-27T03:02:17.311Z
tags: [mysql]
categories: [运维]
---
> 同步开发的表结构到测试环境时报错，后来发现是数据库版本不一致造成的

**报错信息**
```
# [Incorrect table definition;there can be only one TIMESTAMP column with CURRENT_TIMESTAMP in DEFAULT or ON UPDATE clause](https://www.cnblogs.com/joeylee/p/3877578.html)

```
使用 SELECT version(); 确认两个数据库的版本
```
SELECT version();
# 笔者的mysql版本需要>5.6才可以
```
