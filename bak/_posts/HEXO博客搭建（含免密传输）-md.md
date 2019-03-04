title: HEXO博客搭建（含免密传输）.md
date: 2018-09-20 11:41:36
tags: [hexo]
categories: [运维]
---

##### github注册与respositories新建
- 注册github账号
```
https://github.com/
```
- 新建一个respositories
![](https://upload-images.jianshu.io/upload_images/2572206-e0e984c260de9492.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
<!--more-->
![](https://upload-images.jianshu.io/upload_images/2572206-bbfaa099ae6b5bd4.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
![Repository name一定要跟自己的owner一致，并且.github.io后缀](https://upload-images.jianshu.io/upload_images/2572206-2cee92644d185418.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

- 记录下自己的邮箱和用户名，后面我们会用到

##### 本地环境部署
- 配置邮箱和用户名
```
email="asda@qq.com"
owner="gengzongyuan"
```
```
# 设置nodejs版本
version='6.9.0'
wget https://npm.taobao.org/mirrors/node/v${version}/node-v${version}-linux-x64.tar.gz
tar xzf node-v${version}-linux-x64.tar.gz
mv node-v${version}-linux-x64 /usr/local/nodejs

echo 'export NODEJS_HOME=/usr/local/nodejs' >> /etc/profile
echo 'export PATH=$PATH:$NODEJS_HOME/bin' >> /etc/profile
source /etc/profile

# 检查版本
echo checking nodejs:
node -v
echo checking npm:
npm -v
# 设置镜像
npm config set registry=http://registry.npm.taobao.org

# 安装新版本git
yum remove git -y
yum install http://opensource.wandisco.com/centos/6/git/x86_64/wandisco-git-release-6-1.noarch.rpm
yum install git -y

cd ~/.ssh
ssh-keygen -t rsa -C "$email"
```
```
cat ~/.ssh/id_rsa.pub
```
##### github秘钥上传（用于后面的部署）
![](https://upload-images.jianshu.io/upload_images/2572206-9344d32f860af3c9.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
![](https://upload-images.jianshu.io/upload_images/2572206-63ef8018fc54185b.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
![](https://upload-images.jianshu.io/upload_images/2572206-58ff6a9bdf0aa67e.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

##### hexo安装与部署到github
```
# 安装hexo
npm install hexo-cli -g
hexo init /blog
cd /blog
npm install

# 修改配置文件
sed -i "s/yoursite.com/$owner.github.io/g" _config.yml
sed -i "s/theme: landscape/theme: yilia/g" _config.yml
sed -i "s/type:/type: git/g" _config.yml
sed -i "s/title: Hexo/title: $owner's blog/g" _config.yml
sed -i "s/author: John Doe/author: $owner/g" _config.yml
echo "  repo: git@github.com:$owner/$owner.github.io.git" >> _config.yml
echo "  branch: master" >> _config.yml
echo "  message: $owner's blog" >> _config.yml

# 下载主题
git clone https://github.com/litten/hexo-theme-yilia.git themes/yilia

hexo clean
hexo g
hexo d
```



