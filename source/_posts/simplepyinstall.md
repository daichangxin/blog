title: pyinstaller简单打包
date: 2015-10-01 13:38:12
tags: ["python"]
---
下载[pyInstaller](http://sourceforge.net/projects/pyinstaller/) , 解压后不需要执行安装就可以用
下载[pywin32](http://sourceforge.net/projects/pywin32/) , 这个是导出exe依赖的库

写一个bat文件, 内容:
``` bat
@echo off
set /p a=拖入py文件：
python C:\Python27\pyinstaller-2.0\pyinstaller.py -F %a%
pause
```
保存为pyInstaller.bat文件, 双击打开bat文件, 拖入py文件, 按回车就会自动打包成一个独立的exe文件.
其他参数:
``` bat
-F, --onefile Py代码只有一个文件
-D, --onedir Py代码放在一个目录中（默认是这个）
-K, --tk 包含TCL/TK
-d, --debug 生成debug模式的exe文件
-w, --windowed, --noconsole 窗体exe文件(Windows Only)
-c, --nowindowed, --console 控制台exe文件(Windows Only)
-X, --upx 使用upx压缩exe文件
-o DIR, --out=DIR 设置spec文件输出的目录，默认在PyInstaller同目录
--icon=<FILE.ICO> 加入图标（Windows Only）
-v FILE, --version=FILE 加入版本信息文件
```