---
layout: post
title: Gumbo，一个用纯C语言实现的HTML5解析库
date: 2013-08-24
categories:
  - 技术
tags:
  - Gumbo
  - google
  - 工具库
---
[![gumbo](/img/article/2013-08/25-01.jpg)](https://github.com/google/gumbo-parser#gumbo---a-pure-c-html5-parser)

在探讨完Google背后的开源力量后，今天向大家介绍Google开源的一款用C语言实现的HTML5解析库Gumbo，作为一款纯C99库，Gumbo解析时无需任何外部依赖。它主要是用来成为其他工具或库的一个构建块，如linters、验证器、模板语言、重构和分析工具。

目标及特征：

完全符合HTML5规范
强大，并且对于一些有问题的代码，能够灵活、有弹性地处理
简单的API，可以很容易地与其他语言捆绑
支持源位置和指针回到原始文本
轻巧、没有外部依赖
通过所有的html5lib-0.95测试
已在超过25亿个来自谷歌索引的页面中进行过测试

还未实现的目标

执行速度
支持C89
愿望清单（希望不久后能添加进去的功能）

支持最新修订的HTML5规范，以支持模板标签。
支持片段解析
非常全面的错误报告
与其他语言进行绑定
关于Gumbo的更多详情及安装、学习教程，大家可以访问其在Github上的托管地址。


## 扩展阅读

* [[开源推荐]谷歌开源Gumbo：纯C语言实现的HTML5解析库](http://www.csdn.net/article/2013-08-14/2816561-Gumbo-A-pure-C-HTML5-parser)

## 祝大家玩的开心

