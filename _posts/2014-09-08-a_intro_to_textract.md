---
layout: post
title: textract-文本抽取的好工具
date: 2014-09-08
categories:
  - 技术
tags:
  - 文本抽取
---
## textract

[textract](https://github.com/deanmalmgren/textract)，是一块用`python`编写的非常棒的`文本提取`工具。目前支持`.doc`，`.docx`，`.eml`，`.epub`，`.gif`，`.jpg`，`.jpeg`，`.json`，`.html`，`.odt`，`.pdf` 以及 `.png`，`.pptx`，`.ps`，`.txt`，`.wav`，`.xlsx`和`.xls`等格式的文档内容解析提取。从事数据挖掘、分析的同学可以关注一下。比如，你有一堆pdf文档，想要对其内容进行分析挖掘，那可以用textract提取其中的文本，然后进行处理（简单应用应该没问题，大规模的应用尚不能胜任）。当然，在Unix平台,懒得用Word、PPT、xls和xlsx时，也可以巧用textract进行处理（毕竟，Markdown才是主流）。


## 软件安装及命令行使用

```bash

$ pip install textract
```

## 命令行使用

```bash

$ textract path/to/file.extension
```

## 整合python代码

```python

# some python file
import textract
text = textract.process("path/to/file.extension")
```

## 扩展阅读

* [textract源码库](https://github.com/deanmalmgren/textract)
* [textract参考手册](http://textract.readthedocs.org/en/latest/index.html)
* [起草中的Markdown规范](https://github.com/jgm/stmd)

## 祝大家玩的开心

## 编程之道，就在[编程之美]

![编程之美](/img/weixin_qr.jpg)

