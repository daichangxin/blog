title: VSCode 调试 Laya
date: 2019-10-01 15:58:37
tags: ['laya', 'vscode']
---

### 安装插件
https://marketplace.visualstudio.com/items?itemName=msjsdiag.debugger-for-chrome

或 在VSCode插件中搜索：`Debugger for Chrome`

### 使用 Launch 方式启动 Chrome 调试网页
配置 `.vscode/launch.json` 内容：
```
{
    // Use IntelliSense to learn about possible attributes.
    // Hover to view descriptions of existing attributes.
    // For more information, visit: https://go.microsoft.com/fwlink/?linkid=830387
    "version": "0.2.0",
    "configurations": [
        {
            "type": "chrome",
            "request": "launch",
            "name": "Launch Chrome against localhost",
            "url": "http://localhost:8881/bin/index.html",
            "webRoot": "${workspaceFolder}"
        }
    ]
}
```
Launch 方式启动 Chrome 调试网页，本质上是监听 Chrome 打开的网页地址，然后映射到 VSCode 中。所以很多网上教程说配置一下 launch.json 然后就可以直接调试了真是太扯了，因为本地的网页文件并没有使用 web 服务。
所以使用 Launch 方式启动 Chrome Debugger 的前提是，本地先起一个 web 服务器，然后在 launch.json 中的 `url` 配置成这个 web 服务器对应本地要调试文件的地址才行。

### Laya 本地调试准备
编译脚本 (放置路径：`.laya/dev.js`)：
实现监听 ts 文件变化，并自动编译 js 和 map 文件到 `bin/js/` 目录下。
```
const gulp = require('gulp');
const rollup = require('rollup');
const path = require('path');
const node_resolve = require('rollup-plugin-node-resolve');
const typescript = require('rollup-plugin-typescript2');
const glsl = require('rollup-plugin-glsl');
const commonjs = require('rollup-plugin-commonjs');

gulp.task('build', () => {
    return rollup.rollup({
        input: '../src/Main.ts',
        treeshake: true,
        plugins: [
            node_resolve(),
            commonjs(),
            typescript({
                check: false,
                tsconfigOverride: {
                    compilerOptions: {
                        removeComments: true
                    }
                },
                include: /.*(.ts)$/
            }),
            glsl({
                include: /.*(.glsl|.vs|.fs)$/,
                sourceMap: false,
                compress: false
            }),
        ],
        onwarn: function (warning) {
            if (warning.code === 'THIS_IS_UNDEFINED') {
                return;
            }
            console.warn(warning.message);
        }
    }).then(bundle => {
        return bundle.write({
            file: '../bin/js/bundle.js',
            format: 'iife',
            name: 'laya',
            sourcemap: true
        })
    });
});

gulp.task('watch', () => {
    gulp.watch('../src/**/*.ts', ['build']);
});

gulp.task('default', ['watch']);
```

### 调试
使用 `http-server` 起一个简单的 web 服务的脚本，端口号随意指定。
注意：
- 别冲突，冲突了会报错
- 要与 launch.json 中的 url 地址端口相同）

在 package.json 文件的 `scripts`中添加执行命令：
```
"dev": "gulp --gulpfile .laya/dev.js & http-server -p 8881",
```
打开 Terminal ，然后执行：
```
npm run dev
```

打开 VSCode 的调试页签，点击调试，可以断点调试了。