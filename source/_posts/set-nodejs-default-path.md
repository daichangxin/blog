title: 修改nodejs的默认目录
date: 2019-08-18 20:33:34
tags: ['nodejs']
---
新手安装nodejs经常会遇到一些权限问题，要是能有一份nodejs的最佳实践就好了。就好比修改nodejs的默认库安装目录，这样可以避免很多权限问题，特别是Mac系统对一些权限是限制的，导致无法正常安装nodejs类库，修改了安装目录就能解决这里问题。

### 创建目标目录`~/.npm-global`
```
mkdir ~/.npm-global
```
### 设置npm的默认类库目录
```
npm config set prefix '~/.npm-global'
```
### 添加PATH到`~/.profile`文件中，<b>如果没有这个文件，就创建一个</b>，然后添加这行：
```
export PATH=~/.npm-global/bin:$PATH
```
### 执行命令
```
source ~/.profile
```
### 尝试安装一个类库吧~
```
npm install -g jshint
```
### 参考
> [resolving-eacces-permissions-errors-when-installing-packages-globally](https://docs.npmjs.com/resolving-eacces-permissions-errors-when-installing-packages-globally)