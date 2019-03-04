title: Linux多机器互信配置
date: 2019-02-19T13:45:55.070Z
tags: [linux]
categories: [运维]
---
#### 注意项
- /root文件夹权限不能为777
```
chmod -R 700 /root
```
- /root/.ssh权限为700
```
chmod -R 700 /root/.ssh
```
- /root/.ssh/id_rsa权限为600
```
chmod 600 /root/.ssh/id_rsa
```

<!--more-->

#### 多机器配置
> 这里使用了host1,host2,host3三台机器进行互信配置
```
依次在三台机器上执行
ssh-keygen
一路回车即可

host1,host2,host3:依次执行cat /root/.ssh/id_rsa.pub

ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQDYxmRCCAE4UiuXov7CiZ7G3EY0qf1JihUm8MJO9s+Gy9Ko5TyMs4sPfxI6ZRf++lkXwhpyswiA64MJCcCU2mbrbRbJsN84HQ4F+nlnIT7zDyMAQ4itCgdROmR9joPmJlyYC6umemlopziQhvOAA/qnpkpRy3nYt1ou2qwHaYk4TgT10wiX48vIlIoaFoUi9gpMpIgHIJOIpxEdqwdqwdpQMKqYIUUuIIGS0W5b1f0ON9/XcfXlSPFSO26uzyAlqOYyB4NbieB2x54cWrQRGtok4tZNGrfY5yXnwB25Yfl7XnYBu6McNKwQb38s5SUvYGwRJGSHDIv5T5k2rKx9sf root@host1
ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQDYxmRCCAE4UiuXov7CiZ7G3EY0qf1JihUm8MJO9s+Gy9Ko5TyMs4sPfxI6ZRf++lkXwhpyswiA64MJCcCU2mbrbRbJsN84HQ4F+nlnIT7zDyMAQ4itCgdROmR9joPmJlyYC6umemlopziQhvOAA/qnpkpRy3nYt1ou2qwHaYk4TgT10wiX48vIlIoaFoUi9gpMpIgHIJOIpxEdqwdqwdpQMKqYIUUuIIGS0W5b1f0ON9/XcfXlSPFSO26uzyAlqOYyB4NbieB2x54cWrQRGtok4tZNGrfY5yXnwB25Yfl7XnYBu6McNKwQb38s5SUvYGwRJGSHDIv5T5k2rKx9sf root@host2
ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQDYxmRCCAE4UiuXov7CiZ7G3EY0qf1JihUm8MJO9s+Gy9Ko5TyMs4sPfxI6ZRf++lkXwhpyswiA64MJCcCU2mbrbRbJsN84HQ4F+nlnIT7zDyMAQ4itCgdROmR9joPmJlyYC6umemlopziQhvOAA/qnpkpRy3nYt1ou2qwHaYk4TgT10wiX48vIlIoaFoUi9gpMpIgHIJOIpxEdqwdqwdpQMKqYIUUuIIGS0W5b1f0ON9/XcfXlSPFSO26uzyAlqOYyB4NbieB2x54cWrQRGtok4tZNGrfY5yXnwB25Yfl7XnYBu6McNKwQb38s5SUvYGwRJGSHDIv5T5k2rKx9sf root@host3

- 将信息放到一个txt文本中
- 处理好后分别放到三个文件的/root/.ssh/authorized_keys文件中
```









