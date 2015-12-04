---
layout: post
title: libressl - 代码重构的典范
date: 2015-12-04
categories:
  - 技术
tags:
  - libressl
---
## 代码重构的重要性

代码重构已是一个老生常谈的话题了，只是有人在大刀阔斧的进行，有人在一点点修改，还有人依然在犹豫不决的路上…… 还记得 [LibreSSL](http://www.libressl.org/) 吗？就是那个因为 OpenSSL 心脏出血 漏洞，事后 OpenBSD 社区发起的针对 OpenSSL 的重构优化分支。1年过去了，来看看他们都做了什么？


![libressl](/img/article/12/libressl/mgp00003.jpg)

![libressl](/img/article/12/libressl/mgp00004.jpg)

![libressl](/img/article/12/libressl/mgp00005.jpg)


以上图片来自[Bob Beck]大叔的 [LibreSSL, and the new libtls API](http://www.openbsd.org/papers/libtls-fsec-2015/mgp00001.html) 演讲稿。从中可以看出，OpenBSD 社区的重构效果是非常显著的：

* 代码量大幅缩减：删除大量代码，同时进行大幅的重构。总代码约 50 万行，大概是 OpenSSL 的一半；
* 结构调整：将 libssl 从 libcrypto 中分离出来；
* 新功能增加：增加了 libtls 库。增加了 chacha 和 poly1305 等加密算法；
* 可移植性保证：删除了对很多过时了的硬件平台的支持，同时确保在目前主流的平台上与 OpenSSL 兼容；
* 安全性得到了保障：总 bug 少了，还没有严重级别的漏洞。这个表现，确实比 OpenSSL 好多了。


个人觉得，这是 2015 年 OpenBSD 社区所做的最了不起的事，是大家共同努力的结果。他们还计划改造很多其他[项目](http://www.libressl.org/patches.html)，值得期待。其中，代码可读性的改进是最显著的，我也计划进行一次深入的学习和研究。这是一个很好的典范，希望感兴趣的同学，可以进一步跟进学习。

[BoringSSL](https://boringssl.googlesource.com/boringssl) 是 google 的 OpenSSL 分支，重构的更加激进，部分 ABI 都不兼容了，也值得关注。


## 扩展阅读


## 祝大家玩的开心

## 编程之道，就在[编程之美]

![编程之美](/img/weixin_qr.jpg)

