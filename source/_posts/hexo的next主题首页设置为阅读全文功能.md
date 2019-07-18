---
title: hexo的next主题首页设置为阅读全文功能
date: 2019-07-18 16:31:45
tags:
---

next主题首页显示的文章列表，每一篇都是全文，不方便概览，所以想要文章列表只显示预览，添加**阅读全文**功能。

解决方法如下：

- 进入hexo博客项目的themes/next目录
- 用文本编辑器打开_config.yml文件
- 搜索"auto_excerpt",找到如下部分：
- 把enable改为对应的false改为true

其中，length可以设置预览长度