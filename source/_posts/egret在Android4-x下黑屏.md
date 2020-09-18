title: egret在Android4.x下黑屏
date: 2018-01-12 13:44:39
tags: ["Egret"]
---
主要是webGL不支持导致，在`index.html`中判断如果安卓版本过低则使用canvas替代：
```
var renderMode = "webgl";
var index = navigator.userAgent.indexOf("Android 4.")
if (index >= 0) {
    renderMode = "canvas";
}
egret.runEgret({
    renderMode: renderMode,
    audioType: 0
});
```