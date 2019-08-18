title: jenkins配置
date: 2019-02-20 15:35:00
tags: ["jenkins"]
---

## 启动
1. 强烈建议使用jenkins.war文件，并用命令行启动jenkins，这样可以自定义端口号，访问路径后缀，和自定义workspace的目录
2. 可能会提示说java的版本过高，启动时加入参数 `--enable-future-java`来解决
3. 我的启动脚本：

mac:
```
export JENKINS_HOME=/Users/xin/workspace/jenkins
java -jar jenkins.war --enable-future-java --httpPort=8080 --prefix=/jenkins
```
win:
```
set JENKINS_HOME=C:\jenkins
java -jar jenkins.war --enable-future-java --httpPort=8080 --prefix=/jenkins
```

## 账号管理
1. 默认是admin登录，登录密码在`/secrets/initialAdminPassword`中，不要尝试创建用户名为`admin`或`administrator`的账号了
2. 在`系统管理`->`全局安全设置`中启用jenkins用户注册，并勾选`注册用户可做任何事`
3. 注册一个管理员账户，如`admin001`，并进入全局安全设置中把`注册用户可做任何事`取消，选择`项目矩阵授权策略`, 然后勾选`admin001`账户拥有全部权限
4. 到此，可以任何人注册账户，但是没有任何权限，管理员账户可以手动给每个账户加权限


## 乱码问题
1. 执行脚本中有中文输出，显示乱码：
在`系统管理`->`系统设置`->`环境变量`中，增加键值对，key为`LANG`,value为`zh_CN.UTF-8`

2. bat脚本中有乱码
在每个bat脚本执行前，加入一行bat脚本：

`chcp 65001`

## 环境问题
1. 执行时会遇到环境找不到的问题，比如egret编译会提示说找不到引擎：
因为jenkins使用了自己的APP_DATA环境目录，并不是系统默认的，所以找不到各个引擎对应的位置，在jenkins的`环境变量`中增加键值对，key为`APPDATA`，value为`C:\\Users\\你的计算机用户名\\AppData\\Roaming\\`

## Git使用的问题
1. 默认git只拉取一个仓库到本job的工作目录中，如果要拉取多个git:
pipeline的job使用`dir('目录名')`实现
自由风格job使用`Git Plugin`自带的分目录功能:`Additional Behaviours`->`Check out to a sub-directory`即可

2. 提示‘tell me who you are’
执行脚本中加入：
```
git config --local user.email "你的邮箱"
git config --local user.name "你的用户名"
```

3. 账号和密码方式操作https路径的git仓库，会出现卡住而且没有任何报错提示问题
在全局密钥中增加账号密码的密钥，并使用该密钥操作git仓库。其次**仓库的路径要加上用户名**，比如: `https://daichangxin@gitlab.xiangwushuo.com/game/gameBuild.git`

## 其他
1. 调用其他job传参
传参中如果使用单引号，则是直接原封不动的传递过去，如果是双引号，则是先在本job中解析完，再传递过去。先解析的优势是有些变量是只有本job才知道的。
例如：
```
copyFrom = "${JENKINS_HOME}\\workspace\\${JOB_NAME}\\source\\${game_id}\\bin-release\\web\\${client_version}"
copyTo = "${JENKINS_HOME}\\workspace\\gameBuild\\gamebox\\${build_type}\\${game_id}\\"
build job: 'gameBuild', parameters: [string(name: 'copyFrom', value: copyFrom), string(name: 'copyTo', value: copyTo)]
```
2. bat执行参数过长或有空格
路径过长或有空格需要用引号括起来，而且路径要用反斜杠`\\`不能用`/`，否则就会提示参数不正确，因为bat的参数是用斜杠来标识参数名的
复制文件到目标目录中，如果不想被提示说目标是不是目录，则在目标路径后方加上`\\`，如：
```
chcp 65001 & xcopy "source\\%game_id%\\bin-release\\web\\%client_version%" "%JENKINS_HOME%\\workspace\\gameBuild\\gamebox\\%build_type%\\%game_id%\\" /s /h /d /y
```
