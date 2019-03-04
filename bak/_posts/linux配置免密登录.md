title: linux配置免密登录
date: 2018-09-20 12:40:42
tags: [linux]
categories: [运维]
---

```
host1---->host2

进入host1机器
cd ~/.ssh
ssh-keygen -t rsa（四个回车）
ssh-copy-id host2（输入密码，大功告成）

ssh host2
```
