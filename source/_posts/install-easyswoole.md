title: 安装easyswoole
date: 2019-09-07 15:26:36
tags: ["php", "easyswoole"]
---
每次重新部署一台服务器的时候都累到吐血，特别是最近有个很早的服务器到期了，需要把服务搬到另外一个服务器上。
开始吧！
### 安装php7.1
```
// 卸载旧的php
yum remove php* php-common
// 安装源
rpm -Uvh https://dl.fedoraproject.org/pub/epel/epel-release-latest-7.noarch.rpm
rpm -Uvh https://mirror.webtatic.com/yum/el7/webtatic-release.rpm
//查看php源
yum list php*
//开始安装
yum install php71w php71w-opoache php71--cli  php71w-devel
//查看php版本
php -v
```
### 安装swoole
swoole是php的一个扩展，也是easyswoole必须的依赖，需要拉取代码本地编译。
```
// 安装git
yum install git
// 下载swoole代码
git clone https://github.com/swoole/swoole-src.git
// 安装编译相关
yum install php-pear
yum install glibc-headers gcc-c++
```
开始编译了
```
cd swoole-src
phpize
./configure
make && make install
```
修改php配置，添加swoole扩展
```
vim /etc/php.ini
// 添加扩展
extension=swoole.so
```
### 安装mysql5.7
下载
```
wget http://dev.mysql.com/get/mysql57-community-release-el7-7.noarch.rpm
```
安装
```
rpm -ivh mysql57-community-release-el7-7.noarch.rpm
yum install mysql-community-server
```
查看版本
```
mysql -V
```
### 安装composer
```
php -r "copy('https://install.phpcomposer.com/installer', 'composer-setup.php');"
php composer-setup.php
php -r "unlink('composer-setup.php');"
// 设置全局命令
sudo mv composer.phar /usr/local/bin/composer
// 切换阿里镜像
composer config -g repo.packagist composer https://mirrors.aliyun.com/composer/
```

### 安装easyswoole
```
composer require easyswoole/easyswoole=3.x
php vendor/bin/easyswoole install
```

### 安装nginx
```
yum install nginx
// nginx配置路径：/etc/nginx/nginx.conf
```

### 参考
[easyswoole](https://www.easyswoole.com/Cn/Introduction/install.html)