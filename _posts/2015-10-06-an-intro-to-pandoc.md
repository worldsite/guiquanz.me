---
layout: post
title: pandoc - 文档格式转换工具中的王者
date: 2015-10-06
categories:
  - 技术
tags:
  - pandoc
---
## pandoc
 
[pandoc](http://pandoc.org/) 真可谓是，文档格式转换中的瑞士军刀，支持 markdown, reStructuredText, textile, HTML, DocBook, LaTeX, MediaWiki markup, TWiki markup, OPML, Emacs Org-Mode, Txt2Tags, Microsoft Word docx, EPUB 等等格式。详细的格式转换关系，请看下文中的大图。这又是命令行工具党的
好兵器，不容错过。

我主要，使用 pandoc 转换 HTML、reStructuredText 和 markdown，体验非常好。推荐大家使用。


## 安装 (Mac OSX)

详细的安装说明，请[参考官方文档](http://pandoc.org/installing.html)，我这里只针对 Mac OS X 平台。

```bash

$ sudo brew install pandoc
```


## 牛刀小试

* 生成 html 文档 (默认无 header 和 footer)

```bash

$ pandoc -o output.html input.txt
```

* 生成标准的 html 文档 (指定 -s 或 --standalone 标识)

```bash

$ pandoc -s -o output.html input.txt
```

* 从 URI 生成转换文档 (html 转 markdown)

```bash

$ pandoc -f html -t markdown http://www.fsf.org
```

* 从 markdown 转 latex 文档

```bash

$ pandoc -f markdown -t latex hello.txt
```


* 从 html 转 markdown 文档 (遵从严格的 markdown 语法)

```bash

$ pandoc -f html -t markdown_strict hello.html
```


* 从 latex 转 pdf 文档

```bash

$ pandoc test.txt -o test.pdf
```

如果想了解更加详细的内容，请参考[Pandoc User’s Guide](http://pandoc.org/README.pdf)文档。


## pandoc 支持的格式转换图


[![pandoc](/img/article/10/2015-10-06-pandoc.jpg)](http://pandoc.org/)


## 扩展阅读


## 祝大家玩的开心

## 编程之道，就在[编程之美]

![编程之美](/img/weixin_qr.jpg)

