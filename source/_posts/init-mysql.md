title: 初始化mysql
date: 2019-09-07 21:01:21
tags: ['Mysql']
---
CentOS初始化mysql的一些必备的流程记录。
### 启动
```
service mysqld start
```
查看状态：
```
service mysqld status
```
<!-- more -->
### 登录
找到初始密码
```
//先找到安装信息
cat /etc/my.cnf
```
找到`log-error=/var/log/mysqld.log`。
查看这个文件
```
cat /var/log/mysqld.log
```
找到`A temporary password is generated for root@localhost: xxxxxxx`字样。
使用这个密码登录
```
mysql -u root -p
// 回车，输入密码
```

### 设置简单密码（mysql命令环境下）
需要先修改密码策略：
```
set global validate_password.policy=0;  //简单密码
set global validate_password.length=3;  //最低三位
```
执行修改：
```
SET PASSWORD = PASSWORD('123456');
```

### 授权指定ip访问mysql服务器
首先在服务器的安全策略中添加指定ip可访问3306端口。其次，登录mysql，执行语句：
```
GRANT ALL PRIVILEGES ON *.* TO 'root'@'xxx.xxx.xxx.xxx' IDENTIFIED BY '1234' WITH GRANT OPTION;
flush privileges; 
```
如果要任意ip都可访问的话就将地址设置成`%`即可。

参考：
[CentOS 7 64bit下安装并初始化mysql 5.7 完整过程](https://blog.csdn.net/fanpeizhong/article/details/73557202)
[IP地址查询](http://www.ip138.com/)
[Sequel Pro](https://www.sequelpro.com/)