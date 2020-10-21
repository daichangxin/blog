title: 给Hexo添加百度统计
date: 2020-09-22 09:38:07
tags: ["Hexo"]
---
原理是在Hexo的header代码中，添加js统计脚本。
### 1、百度统计注册账号，获取统计脚本
https://tongji.baidu.com/

### 2、Hexo主题配置修改
修改主题配置文件：`themes/{你使用的主题}/_config.yml`，添加一个开关配置：
```
# baidu
baidu_analytics: true
```

### 3、新建模版
新建 `themes/{你使用的主题}/layout/_partial/baidu_analytics.ejs` ：
```
<% if (theme.baidu_analytics) { %>
    <script type="text/javascript">
    #申请的百度统计代码
    </script>
<% } %>
```
然后编辑 `themes/{你使用的主题}/layout/_partial/head.ejs` 在最上方添加
```
<%- partial("baidu_analytics") %>
```
重新部署即可。