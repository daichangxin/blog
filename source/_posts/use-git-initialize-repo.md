title: git将本地代码提交到远程仓库
date: 2017-03-15 10:55:53
tags: ["Git"]
---
##### 本地初始化：
```
git init
```

##### 连接远程仓库
```
git remote add origin https://git.coding.net/greatxin/jenkins.git
```

##### 拉取一次
```
git pull origin master

```

##### 加入本地文件并提交
```
git add .
git commit -m "init"
git push origin master
```