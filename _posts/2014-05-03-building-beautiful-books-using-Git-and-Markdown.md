---
layout: post
title: gitbook-用Markdown和GitHub/Git制作精美的电子书
date: 2014-05-03
categories:
  - 技术
tags:
  - gitbook
---
## gitbook简介

[![GitBook](/img/article/05/2014-05-03_01.png)](http://www.gitbook.io)

[Markdown](http://daringfireball.net/projects/markdown/syntax)是，一种极简的文本编辑格式，是geek们的好工具。用Markdown写blog（我blog平台也是基于Markdown的），用Markdown写`幻灯片`(参考[巧用keydown和markdown编写基于文本的幻灯片](http://guiquanz.me/2013/01/31/make-ppt-by-keydown-and-markdown/))，用Markdown写软件手册……。Markdown只是一种文本格式，穿什么花衣，用什么工具进行渲染，完全由使用者决定。Markdown很好用，也非常流行，是每个文字工作者和程序员，都应该学习和使用的格式。今天，给大伙介绍一种新的玩法——`用Markdown和GitHub/Git制作精美的电子书`。

这里我们需要一个由于[GitBook](http://www.gitbook.io/)公司，开源的工具[gitbook](https://github.com/GitbookIO/gitbook/)。`gitbook`是基于[nodejs](http://nodejs.org/)平台的工具，所以在使用之前，需要安装nodejs（此处不再介绍）。


## 安装gitbook

```bash
    $ sudo npm install gitbook -g
```

## 启动服务器

```bash
   $ gitbook serve -p 8080 ./repository
```

或

```bash
    $ gitbook build ./repository --output=./outputFolder
```

## 浏览精美的图书

执行`gitbook serve -p 8090 . `命令，会编译你的Markdown文档，生成一个`_book`目录， 然后默认在`4000`(我们通过`-p`指定的端口是`8090`)端口启动http监听服务。通过`http://localhost:8090`便可以浏览你的图书了。来看一下，我整理的`Google C++ Style Guide`，很酷吧！

[Google C++ Style Guide](http://guiquanz.gitbooks.io/google-cc-style-guide/)一书实际效果，如下：

[![Google C++ Style Guide](/img/article/05/2014-05-03_02.png)](http://guiquanz.gitbooks.io/google-cc-style-guide/)


## 生成pdf文档

目前`gitbook`还支持`PDF`格式的文档生成，只需安装[gitbook-pdf](https://github.com/GitbookIO/gitbook-pdf)工具即可。此处不再赘述，大家自己动手去实践吧。（btw: 我现在的文档和编写中的图书，都是使用`Markdown`、`Git`和`gitbook`维护者，以后找机会分享）


## 祝大家玩的开心

## 编程之道，就在[编程之美]

![编程之美](/img/weixin_qr.jpg)

