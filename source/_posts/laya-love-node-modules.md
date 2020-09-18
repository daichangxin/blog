title: layabox使用node_modules
type: tags
layout: tags
date: 2019-08-25 11:29:05
tags: ['Nodejs', 'Layabox']
---
用习惯了egret、layabox这类游戏引擎的人，很少有关注node_modules类库，或者用到的时候不得不用各种曲折的方法，比如在egret中如果要引用一个类库，必须把这个类库放到指定的目录中去，如果默认没有.min.js文件，还要拷贝一个命名成.min.js文件，再然后把对应的type文件放到目中去，最终的结构就像这样：
```
libs/thirdLib/thirdLib.js
libs/thirdLib/thirdLib.min.js
libs/thirdLib/thirdLib.d.ts
```

Laya也一样，需要再index.js中把类库引用进去，然后再把.d.ts文件放到libs的声明文件中。

对于很多nodejs开发者来说，就非常需要node_modules包来管理了。庆幸的是Laya其实没有对编译做各种绑定和预处理，我们完全可以修改下编译脚本就能实现引用打包node_modules类库了。

这里使用<code>Moment.js</code>来举个🌰。
<!-- more -->
### 添加类库
```
npm init
npm install moment --save
```

### 修改编译选项
`tsconfig.json`中添加
`"moduleResolution": "node"`，如果不修改就直接import包的话，会提示找不到模块。
`allowSyntheticDefaultImports`是因为有的类库会提示没有默认导出报错，比如现在用的moment.js就是。
```
{
  "compilerOptions": {
    "module": "es6",
    "target": "es6",
    "noEmitHelpers": true,
    "sourceMap": false,
    "moduleResolution": "node"，
    "allowSyntheticDefaultImports": true
  },
  "exclude": [
    "node_modules"
  ]
}
```

### 代码中使用
导入：
```
import moment from 'moment';
```
不要使用 `import * as moment from 'moment';` momentjs这样导入会有一堆的编译问题，比较奇葩，因为它既是namespace，又是函数体。

使用：
```
moment().format('MMMM Do YYYY, h:mm:ss a')
moment().format('dddd');
moment().format("MMM Do YY");
moment().format('YYYY [escaped] YYYY');
moment().format();
```

### 编译
这时候直接编译会提示报错：
```
error: 'moment' is imported by ../../../
```
安装两个编译脚本需要用到的库：
```
npm i rollup-plugin-node-resolve rollup-plugin-commonjs -D
```
`rollup-plugin-node-resolve`主要解决编译导入node_modules类库的问题
`rollup-plugin-commonjs`主要解决node类库中各种`glboal`等语法在浏览器中不支持的问题。
最终修改脚本如下：
```
// v1.1.0
//是否使用IDE自带的node环境和插件，设置false后，则使用自己环境(使用命令行方式执行)
let useIDENode = process.argv[0].indexOf("LayaAir") > -1 ? true : false;
//获取Node插件和工作路径
let ideModuleDir = useIDENode ? process.argv[1].replace("gulp\\bin\\gulp.js", "").replace("gulp/bin/gulp.js", "") : "";
let workSpaceDir = useIDENode ? process.argv[2].replace("--gulpfile=", "").replace("\\.laya\\compile.js", "").replace("/.laya/compile.js", "") : "./../";

const gulp = require(ideModuleDir + "gulp");
const rollup = require(ideModuleDir + "rollup");
const typescript = require(ideModuleDir + 'rollup-plugin-typescript2');//typescript2 plugin
const glsl = require(ideModuleDir + 'rollup-plugin-glsl');
const resolve = require('rollup-plugin-node-resolve');
const commonjs = require('rollup-plugin-commonjs');

// 如果是发布时调用编译功能，增加prevTasks
let prevTasks = "";
if (global.publish) {
	prevTasks = ["loadConfig"];
}

//使用browserify，转换ts到js，并输出到bin/js目录
gulp.task("compile", prevTasks, function () {
	// 发布时调用编译功能，判断是否点击了编译选项
	if (global.publish && !global.config.compile) {
		return;
	} else if (global.publish && global.config.compile) {
		// 发布时调用编译，workSpaceDir使用publish.js里的变量
		workSpaceDir = global.workSpaceDir;
	}

	return rollup.rollup({
		input: workSpaceDir + '/src/Main.ts',
		treeshake: true,//建议忽略
		plugins: [
			typescript({
				check: false, //Set to false to avoid doing any diagnostic checks on the code
				include: /.*(.ts)$/,
				tsconfigOverride: { compilerOptions: { removeComments: true } }
			}),
			glsl({
				// By default, everything gets included
				include: /.*(.glsl|.vs|.fs)$/,
				sourceMap: false,
				compress: false
			}),
			resolve(),
			commonjs(),
		],
		onwarn: function (warning) {
			if (warning.code === 'THIS_IS_UNDEFINED') { return; }
			console.warn(warning.message);
		}
	}).then(bundle => {
		return bundle.write({
			file: workSpaceDir + '/bin/js/bundle.js',
			format: 'iife',
			name: 'laya',
			sourcemap: false
		});
	});
});
```

### 输出
```
August 25th 2019, 10:05:10 pm
Sunday
Aug 25th 19
2019 escaped 2019
2019-08-25T22:05:10+08:00
```
### 源码
[laya-love-node_modules](https://github.com/daichangxin/laya-love-node_modules)

### 备注
有的类库还会有低版本不兼容的问题，解决无非就是找各种roleup插件来解决，比如[rollup-plugin-babel](https://github.com/rollup/rollup-plugin-babel);