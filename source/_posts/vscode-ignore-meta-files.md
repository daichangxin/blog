title: VSCode 屏蔽 meta 文件
date: 2020-12-07 15:38:14
tags: ['VSCode']

---

解决步骤：
从顶部菜单栏打开文件-> 首选项-> 设置（或者使用快捷键 `Ctrl+,`） 打开设置页面
在设置页面的输入框中输入 `files:exclude`，
在设置页面查找 `file: exclude`.
点击添加模式，输入`**/*.meta` 即可
