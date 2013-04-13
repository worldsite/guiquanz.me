---
layout: post
title: libngx，将nginx数据结构打包为一个静态库
date: 2013-04-13
categories:
  - 技术
tags:
  - libngx
  - nginx
---
## 

`Nginx`是一块轻量级、高性能的Web服务器软件，因此备受青睐。其实，Nignx的实现更是精妙，从编码规范，通用数据结构（列表、内存池等）、系统构建方面来说都是非常好的典范，值得学习。

如果你正在研究nginx，想使用其中的数据结构，能将其编译为静态库那就好了。其实，只需要修改auto工具链就可以了。早前我已完成此工作，看代码[libngx](https://github.com/guiquanz/libngx)。当然，此项目还处于早期开发阶段，不建议在产品中使用，后续会有非常大的变动。

我的目标是，开发一个通用的数据结构和算法库，遵循Nginx的编码风格（深受其害，哈哈），类似《C语言接口设计与实现》(`C Interfaces and Implementations: Techniques for Creating Reusable Software`)的库[cii](http://code.google.com/p/cii/)，但更全面及实用。大家拭目以待吧。


## 扩展阅读

* [ningx代码研究](http://code.google.com/p/nginxsrp/wiki/NginxCodeReview)
* [nginx源码分析](http://blog.csdn.net/livelylittlefish/article/details/6636229)
* [libngx: https://github.com/guiquanz/libngx.git](https://github.com/guiquanz/libngx.git)

## 祝大家玩的开心


