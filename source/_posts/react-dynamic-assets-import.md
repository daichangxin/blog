title: React动态导入资源
date: 2020-09-21 19:50:07
tags: ["React"]
---
React在导入资源的时候，只能指定资源的路径，如果要实现动态的路径，比如本地的任务icon列表，需要读取icon的地址是：
```
<img alt="" src={`../assets/images/taskIcons/task_${taskID}.png`}></img>
```
这样的写法会在webpack打包后，变成固定的字符串，而不会被webpack的资源hash处理，这就导致编译后资源地址读取错误。
解决的办法是利用webpack的`require.context`，原理是在编译阶段读取整个目录的文件，并将路径嵌套在代码中。
首先安装声明
```
npm install @types/webpack-env -D
```
指定require资源目录：
```
const assets = require.context('../assets');
```
提供一个动态获取资源地址的接口：
```
export const requirePath = (assets: __WebpackModuleApi.RequireContext, name: string) => {
    const key = `./${name}`;
    return assets.keys().includes(key) ? assets(key).default : name;
};
```
使用：
```
<img alt="" src={requirePath(`images/taskIcons/task_${taskID}.png`)}></img>
```
注意，在使用`require.context`时参数必须是字符串值，不可以用变量，因为是在编译阶段生成的路径映射，使用变量是无法生成的。