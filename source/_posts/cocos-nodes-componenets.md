title: Cocos 的 node 与 component
date: 2020-12-07 14:52:36
tags: ['CocosCreator']

---

今日开始入 Cocos 开发。
之前打开过 Unity 软件，一直不太明白 `getChild` 与 `getComponent` 到底有什么区别，万一有多个重名的组件怎么办呢？
做个 Demo 一步步分析，在 Canvas 中设置了一个 ProgressBar，名字也叫 ProgressBar，我想获取到这个 ProgressBar：

```
const bar = this.node.getComponenet(ProgressBar);
console.log(bar); // output: null
```

通过断点发现，调用的 `getComponent`，获取到的是右侧属性面板中的“组件”，而不是“子节点”，在场景中的每个对象身上挂载的组件，都可以通过 `getComponent` 来获取，有点类似把所有的属性都封装成组件，由组件来实现对象的行为。
这其实就是 ecs 的东西了，所有的对象都是空的，至于对象表现成什么取决于它身上挂载的组件，比如对于所有的显示对象，身上都会挂载一个 `Transform` 的组件来表示位移、缩放等信息。
而 `getChildByName` 才是获取当前 node 下的子对象，也就是左边场景编辑器中的子节点对象。另外 `getChildByName` 是通过 for 循环来获取的，`getChildByUuid` 也是 for 循环。这也是为啥推荐“将子对象绑定”来实现快捷子对象访问了，即在组件上定义要绑定的其他组件，然后在场景编辑器中将被绑定的组件拖过去就可以了。示例:
在这个 demo 中，组件定义：

```
@property({ type: ProgressBar })
bar: ProgressBar = null;
```

在编辑器中：
<img src="/images/cocos-bind-node.png" width="50%" text-align="center">
这样组件定义中就可以通过 bar 直接访问到该 ProgressBar 了。
