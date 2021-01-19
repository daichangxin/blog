title: 使用Github Action自动发布NPM包
date: 2020-09-18 14:07:55
tags: ["Git", "Nodejs"]
---
## 准备NPM的部署token
官网 https://www.npmjs.com/ 注册账号，然后在`Auth Tokens`下创建`Access Tokens`，这个token是给`Gitlab Action`使用来实现NPM包发布用的。

## Github绑定Access Token
个人用户：在Github中，打开`Settings->Developer settings->Personal access tokens->Generate new token`，名字将来作为env的一个变量可以读取的到。

组织账户：打开`Settings->Secrets->New secret`

部署脚本
在仓库根目录下，创建目录`.github/workflows`，然后在这个目录下创建配置文件：npm-publish.yml，内容如下：
<!-- more -->
```
# This workflow will run tests using node and then publish a package to GitHub Packages when a release is created
# For more information see: https://help.github.com/actions/language-and-framework-guides/publishing-nodejs-packages

name: Node.js Package

on:
  push:
    branches:
      - master

jobs:
  auto_release_npm_package:
    if: "!contains(github.event.head_commit.message, 'skip ci')"
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v2
      - uses: actions/setup-node@v1
        with:
          node-version: 12
      - run: |
          git config --local user.email "action@github.com"
          git config --local user.name "GitHub Action"
          git checkout $CI_COMMIT_REF_NAME
          npm config set //registry.npmjs.org/:_authToken=$NODE_AUTH_TOKEN
          npm config set scope "@pawgame"
          npm config list
          npm ci
          npm run build
          SPACE_CHAR=" "
          npx standard-version --releaseCommitMessageFormat "chore(release):${SPACE_CHAR}{{currentTag}} [skip ci]"
          git push --follow-tags origin $CI_COMMIT_REF_NAME
          npm publish --access public
        env:
            NODE_AUTH_TOKEN: ${{secrets.NPM_NODE_AUTH_TOKEN}}
```
## 逐行分析：
### 触发条件：
```
on:
  push:
    branches:
      - master
```
表示在master分支上，收到push行为时自动触发该脚本

### 跳过触发：
```
if: "!contains(github.event.head_commit.message, 'skip ci')"
```
表示如果提交信息中包含了`'skip ci'`，就跳过脚本执行，因为经常会修改配置或者项目说明，是不需要重复发布包版本的。

### 配置脚本执行环境：
```
runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v2
      - uses: actions/setup-node@v1
        with:
          node-version: 12
```

### 执行脚本内容：
```
npm config set //registry.npmjs.org/:_authToken=$NODE_AUTH_TOKEN
npm config set scope "@pawgame"
npm config list
npm ci
```
这里的`$NODE_AUTH_TOKEN`，是最后一行env中定义的变量，直接获取secrets中的变量，因为我发布的是组织pawgame的包，所以使用的是secrets中配置的token，并设置scope为`"@pawgame"`

约定式提交：
参考 https://www.conventionalcommits.org/zh-hans/v1.0.0-beta.4/ , 这里使用`standard-version`这个工具来自动生成CHANGELOG和打tag，自动修改包的版本号。