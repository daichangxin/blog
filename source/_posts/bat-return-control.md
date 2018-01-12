title: bat脚本不返回控制权
date: 2017-03-15 11:01:11
tags: [脚本, jenkins]
category: 脚本
---
bat调用bat时, 控制权不会返回到第一个bat脚本, 导致第二个执行完就结束了, 需要加上call来调用。这里是jenkins的启动脚本，在启动jenskin前会先同步一次job和scripts库。
```
@echo off
set JENKINS_HOME=D:\dev\jenkins
cd /d %JENKINS_HOME%

call jenkins_updateScripts.bat

echo "restart Jenkins: http://localhost:1201"
net stop jenkins
java -jar %JENKINS_HOME%\jenkins.war --httpPort=1201

pause
```