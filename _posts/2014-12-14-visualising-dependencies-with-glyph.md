---
layout: post
title: 用glyph可视化go package之间的依赖关系
date: 2014-12-14
categories:
  - 技术
tags:
  - glyph
---
## glyph

[glyph](https://godoc.org/github.com/davecheney/junk/glyph)是[Dave Cheney](http://dave.cheney.net/about)为了分析[juju](https://jujucharms.com/)核心代码包的依赖关系，便于松耦重构时创建的一个工具。`glyph`是一个go程序，其实现依赖[sigmajs](http://sigmajs.org/)这个javascript库来绘制依赖关系图。目前支持`tree`,`radial`,`forcegraph`和`chord`四种显示模式。

以为`net`包为例，其依赖关系可视化结果，如下：

![tree](/img/article/12/2014-12-14-01-glyph-tree-net.png)

![radial](/img/article/12/2014-12-14-01-glyph-radial-net.png)

![forcegraph](/img/article/12/2014-12-14-01-glyph-forcegraph-net.png)

![chord](/img/article/12/2014-12-14-01-glyph-chord-net.png)

`glyph`是一个基于浏览器的程序，默认会在`8080`端口上启动监听服务。可以用于分析go语言自己的标准包，也可以分析自己的代码。其代码实现不复杂，拒绝`伸手党`，请自己动手琢磨怎么安装和使用。

## 扩展阅读

* [Visualising dependencies](http://dave.cheney.net/2014/11/21/visualising-dependencies)
* [Force-Directed Graph](http://bl.ocks.org/mbostock/4062045)
* [sigmajs](http://sigmajs.org/)

## 祝大家玩的开心

## 编程之道，就在[编程之美]

![编程之美](/img/weixin_qr.jpg)

