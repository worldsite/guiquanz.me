---
layout: post
title: 你应该知道的几个C语言字符串处理库
date: 2014-09-13
categories:
  - 技术
tags:
  - C语言字符串处理库
---
## C的一大痛点

__c__依然是我的首选系统编程语言，性能优先。但是C一直都有很多众所周知的病痛，比如没有通用数据结构及算法的实现，没有系统级别的应用框架（如通用数据库层等）等等。由于各种派系的风化，最后连最基础的东西都没能提供。比如，安全高效的字符串处理等。从现在（C11）和未来,长期来看也不太可能去解决这些问题了。也许是出于各种无奈，`Thompson`C语言的发明人，也只好领着`Pike`、`Russ Cox`等一帮Googlers，去开发go语言，美其名曰"21世纪的C语言"，以期解决新时期针对多核的编程问题了。当然，这依然还是漫漫长路…… （等等：其实，C++字符串处理存在的问题更严重，还是改天讨论吧）

相信很多人都跳过`strcpy`、`strcat`和`strdup`等相关的坑。以下介绍几个试图更好的解决C字符串处理的库，以供参考。

* redis sds - Simple Dynamic Strings library for C

> [sds - Simple Dynamic Strings library for C](https://github.com/antirez/sds)
> 
> [internals-sds](http://redis.io/topics/internals-sds)
 
* GNOME GString

>  [String Utility Functions — various string-related functions](https://developer.gnome.org/glib/stable/glib-String-Utility-Functions.html)

* libphenom - library of awesome for C apps

>  [libphenom](https://github.com/facebook/libphenom)


* The Better String Library
 
>  [bstring](http://bstring.sourceforge.net)
> 
>  [bstring on github](https://github.com/guiquanz/bstring)
> 
>  [bstring manual](http://mike.steinert.ca/bstring/doc/bstrlib_8h.html)
> 
>  [stmd - using of bstring](https://github.com/jgm/stmd)
> 

* safe-string

>  [safe-string](https://github.com/sghost/safe-string)

* The Safe C Library

>  [the safe c library](http://www.drdobbs.com/cpp/the-safe-c-library/214502214)
> 
>  [safeclib](http://sourceforge.net/projects/safeclib/)
> 


## 扩展阅读

* [libPhenom manual](http://facebook.github.io/libphenom/)

## 祝大家玩的开心

## 编程之道，就在[编程之美]

![编程之美](/img/weixin_qr.jpg)

