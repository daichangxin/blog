title: 深入浅出 TypeScript
date: 2019-09-13 09:56:37
tags: ['typescript']
---
用了许久的ts，发现还是有很多语法糖和特性都不知道。
### 文件模块
commonjs, amd, es modules, others
在 `tsconfig.json` 中设置不同的 `module` 选项来把ts编译成不同的目标 js 模块类型：
- AMD: 仅在浏览器中使用，不要使用
- SystemJS: 已被ES取代
- ES: 未准备好

所以推荐的写法是：
使用 `module: commonjs` 选项以及使用 ES 模块语法导入导出其他模块。

在实际项目中，会使用 `node_modules` 中的类库，就需要在 `tsconfig.json` 中设置 `moduleResolution: node` ，如果使用了 `module:commonjs` 选项，那么 `moduleResolution: node` 是默认开启的，不需要专门再设置。


## TODO