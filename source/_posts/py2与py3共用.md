title: py2与py3共用
date: 2018-01-17 21:08:22
tags: ["python"]
---

### 编译

同时安装python2与python3后，使用python2运行：
```
py -2 hello.py
```
使用python3运行：
```
py -3 hello.py
```

如果不想每次运行都加入参数`-2`和`-3`，可以把启动类型写入py文件中，python2文件在代码开头加入：
```
#! python2
```
python3则是在代码开头加入：
```
#! python3
```
<!-- more -->
### 使用pip

python2命令：
```
py -2 -m pip install XXXX
```
python3命令：
```
py -3 -m pip install XXXX
```

### #! python2 和 # coding: utf-8 哪个写在前面？

#! python2 需要放在第一行，编码说明可以放在第二行。所以文件开头应该类似于：
```
#! python2
# coding: utf-8
```