---
layout: post
title: 解决 Mac OS X 平台 Sublime Text 中文乱码问题
date: 2015-10-30
categories:
  - 技术
tags:
  - 中文乱码
---
## Sublime Text

[Sublime Text]()，是一块好工具，也是我用的最多的那一个编辑器，花钱买的，不用就浪费了。本文简单说说在 Mac OS X IE C (10.11.1) 下中文乱码问题及解决方案。

Mac OS X 属于 Apple 独家演绎的 Unix 分支版本，默认使用 UTF-8 编码，当使用不同开发平台的小伙伴们，共同维护一份代码的时候，尤其现在很多人都还在用 Windows 系统的时候，由于 Linux 和 Unix 都不支持 GBK 和 GB2312 的中文编码，所以遇见乱码是在所难免的。毕竟，又不能强迫大家都是用`英文`书写。这时候，各种插件就派上用场了。 好在 Sublime Text 有非常丰富的开源插件，帮忙解决很多日常问题。


## 安装 ConvertToUTF8 插件

Sublime Text 默认是不支持中文编码的，所以需要手工安装 [ConvertToUTF8]() 插件，便可以正常的支持中文输入了。


## 安装 GB2312 插件

如果还要支持 Windows 平台默认的 GB2312 中文编码，那么需要安装 [Codecs33]() 插件：

一般打开 Windows 平台的文件时，会显示如下的提示信息：

```text

File: /Users/xxx/yyy.cc
Encoding: GB2312
Error: Codecs missing

Please install Codecs33 plugin (https://github.com/seanliang/Codecs33/tree/osx).

```

__针对不同的平台，相应的插件可能存在差异。一般安装对应的插件即可。__


有了这些插件的支持，跨平台的协调编辑就不在是问题了。感谢开源，感谢开源背后的贡献者们。


## 扩展阅读


## 祝大家玩的开心

## 编程之道，就在[编程之美]

![编程之美](/img/weixin_qr.jpg)

