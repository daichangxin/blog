title: Nginx上手
date: 2020-09-18 10:25:56
tags: ["Nginx"]
---
## 安装方式
一开始使用Docker来安装，参考的是 http://www.ruanyifeng.com/blog/2018/02/nginx-docker.html 这篇教程的文章，后来发现Nginx作为子容器访问宿主机端口非常的麻烦，尝试了好几个网上的教程都不理想，而Nginx用来做反向代理对我来说是刚需，索性外部独立安装吧。

## Yum安装尝试
看教程里别人安装都很顺利，步骤是：
```
// 安装EPEL软件仓库
yum install epel-release
// 输出：No package epel-release available.
```
找了各种方式终于安装`epel-release`成功后，执行命令：
```
yum install nginx
// 输出：No package nginx available.
```
参考官方的教程：https://www.nginx.com/resources/wiki/start/topics/tutorials/install/。
查看下系统的版本：
```
cat /etc/redhat-release
// 输出: CentOS Linux release 7.7.1908 (Core)
```
于是在/etc/yum.repos.d/nginx.repo下创建文件nginx.repo，内容为：
```
[nginx]
name=nginx repo
baseurl=https://nginx.org/packages/centos/7/$basearch/
gpgcheck=0
enabled=1
```
执行`yum install nginx`，依然提示`No package nginx available.`

## 手动安装Nginx
仔细看官方的配置其实是指定了系统的版本号, 来查找对应的Nginx版本，查找下`$basearch`：
```
rpm -qf /etc/*-release
// 输出
centos-release-7-7.1908.0.el7.centos.x86_64
centos-release-7-7.1908.0.el7.centos.x86_64
centos-release-7-7.1908.0.el7.centos.x86_64
centos-release-7-7.1908.0.el7.centos.x86_64
```
即`$basearch`是`x86_64`，直接浏览器打开 https://nginx.org/packages/centos/7/x86_64/RPMS/ , 复制最新版的rpm地址，当前最新为:
https://nginx.org/packages/centos/7/x86_64/RPMS/nginx-1.10.0-1.el7.ngx.x86_64.rpm
在当前目录下载这个文件：
```
wget https://nginx.org/packages/centos/7/x86_64/RPMS/nginx-1.10.0-1.el7.ngx.x86_64.rpm
```
然后手动安装
```
rpm -i nginx-1.10.0-1.el7.ngx.x86_64.rpm
// 输出
warning: nginx-1.10.0-1.el7.ngx.x86_64.rpm: Header V4 RSA/SHA1 Signature, key ID 7bd9bf62: NOKEY
----------------------------------------------------------------------

Thanks for using nginx!

Please find the official documentation for nginx here:
* http://nginx.org/en/docs/

Commercial subscriptions for nginx are available on:
* http://nginx.com/products/

----------------------------------------------------------------------
```
安装成功！


## 常用命令

#### 检查配置是否正确
```
nginx -t
```

#### 重启
```
nginx -s reload
```
