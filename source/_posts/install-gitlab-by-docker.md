title: 使用Docker安装Gitlab
date: 2020-09-18 11:38:46
tags: ["Docker", "Gitlab"]
---
## 拉取镜像
```
docker pull gitlab/gitlab-ce:latest
```

## 创建容器脚本（`docker_gitlab_run.sh`）
```
docker container run \
    -d \
    -p 6100:80 \
    -it \
    --name gitlab \
    --restart always \
    --volume ~/data/gitlab:/etc/gitlab \
    --volume ~/data/log/gitlab:/var/log/gitlab \
    --volume ~/data/opt/gitlab:/var/opt/gitlab \
    gitlab/gitlab-ce:latest
```
这里对外端口为6100，查看下docker启动的状态：
```
docker ps -a
```
等启动完成，gitlab就创建好了。
<!-- more -->
## 修改配置
配置修改一律通过`~/data/gitlab/gitlab.rb`这个文件来修改，即镜像中的`/etc/gitlab/gitlab.rb`文件。

默认地址是http://244788b062fc/administrator/test.git ，前面的数字是gitlab容器的id，需要修改为自己的域名地址，修改`gitlab.rb`文件中的：
```
external_url 'http://yourhostname:6100'
```
这里修改为6100端口后，会导致`nginx`也会监听6100端口，继续修改配置：
```
nginx['listen_port'] = 80
```
保存后，执行刷新gitlab配置命令：
```
docker exec -t gitlab gitlab-ctl reconfigure
```
重启gitlab：
```
docker exec -t gitlab gitlab-ctl restart
```