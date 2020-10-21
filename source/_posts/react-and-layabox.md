title: React与Layabox混合开发实现
date: 2020-09-21 20:08:59
tags: ["React", "Layabox"]
---
### 1、图片加载的问题
使用import的地址，但是无法解决批量加载的问题，参考： https://blog.pawgame.com/2020/09/21/react-dynamic-assets-import 
```
npm i -D @types/webpack-env
```
使用：
```
const assets = require.context('../../imgs', true);
Laya.URL.customFormat = (url: string) => {
    return assets.keys().includes(`./${url}`) ? assets(`./${url}`).default : url;
};
```

### 2、canvas鼠标滑动问题
修改laya.core.js，注释部分代码：
```
canvas.addEventListener("touchstart", function (e) {
    if (MouseManager.enabled) {
        // if (!MouseManager._isFirstTouch && !Input.isInputting)
        //     (e.cancelable) && (e.preventDefault());
        _this.mouseDownTime = Browser.now();
        _this.runEvent(e);
    }
});
canvas.addEventListener("touchend", function (e) {
    if (MouseManager.enabled) {
        if (!MouseManager._isFirstTouch && !Input.isInputting)
            (e.cancelable) && (e.preventDefault());
        MouseManager._isFirstTouch = false;
        _this.mouseDownTime = -Browser.now();
        _this.runEvent(e);
    }
    else {
        _this._curTouchID = NaN;
    }
}, true);
canvas.addEventListener("touchmove", function (e) {
    if (MouseManager.enabled) {
        // (e.cancelable) && (e.preventDefault());
        _this.runEvent(e);
    }
}, true);
```

### 3、鼠标点击错位问题
修改laya.core.js，增加getBoundingClientRect检测：
```
initEvent(e, nativeEvent = null) {
    var _this = this;
    _this._event._stoped = false;
    _this._event.nativeEvent = nativeEvent || e;
    _this._target = null;
    const {left, top} = e.target.getBoundingClientRect();
    this._point.setTo((e.pageX || e.clientX) - left, (e.pageY || e.clientY) - top);
    if (this._stage._canvasTransform) {
        this._stage._canvasTransform.invertTransformPoint(this._point);
        _this.mouseX = this._point.x;
        _this.mouseY = this._point.y;
    }
    _this._event.touchId = e.identifier || 0;
    this._tTouchID = _this._event.touchId;
    var evt;
    evt = TouchManager.I._event;
    evt._stoped = false;
    evt.nativeEvent = _this._event.nativeEvent;
    evt.touchId = _this._event.touchId;
}
```
