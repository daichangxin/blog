title: FlashBuilder闪退
date: 2015-10-01 13:50:35
tags: ["flash"]
---
今天遇到打开FlashBuilder秒退, 网上找到了解决办法:
进入工作目录下
```
.metadata/.plugins/org.eclipse.core.resources
```

删除.snap文件
如果是苹果系统，则目录有可能是：

```
.metadata\org.eclipse.core.resources
```