---
layout: post
title: Fedora x86_64平台mysql-3.23.49编译总结
date: 2014-08-16
categories:
  - 技术
tags:
  - mysql
---
## 小折腾

第一次修改[mysql](http://www.mysql.com/)源代码，那是好几年前的事情了。记得客户端`mysql`上键入以`；`结束的空行时，总是报错，因为这是输入查询行的结束标志。用的不爽，就操刀给改了。后来，还陆续研究过`5.1+`的版本，还写过一些简单的存储引擎……最近刚好拿了一本`姜承尧`的《MySQL内核——InnoDB存储引擎》书来看，但此书是以`mysql-3.23.49`版本的`InnoDB`存储引擎为研究对象，索性就安装测试一下。由于这是非常老的一个MySQL版本（差不多是网上能找到的最古老的版本了）。这代码是针对32位系统（当时64位系统还没有出现）的实现，而我实际是在`Fedora Linux x86_64`平台编译的，期间撞上了很多问题，统一总结于此，仅供参考。


## 我的测试系统环境

这里先罗列一下我的系统环境。 `Fedora 19 Linux x86_64`系统，编译器版本为`GCC 4.8.2`（这个版本应该没有影响。按照姜的说法，在Ubuntu 12.0.4上，需要安装`gcc 3.x`版本。我看应该也不必要。现在主流的也是4.x的版本了，都应该没问题。由于我没有Ubuntu的环境，暂时就去验证了）。

## 编译准备

* 安装32位系统头文件

```bash

$ sudo yum install -y glibc-devel.i686
$ sudo yum install -y ncurses-libs.i686
$ sudo ln /usr/lib/libtinfo.so.5 /usr/lib/libtinfo.so # 如果编译时出现找不到32位的`tinfo`，需要执行此操作
$ sudo yum install -y zlib-devel-1.2.8-6.fc22.i686 # 可以源码安装,别忘了 -m32
```

## 系统编译和问题解决

* 1、如果直接执行 `./configure`可能会报类似以下的错误

```bash

configure: error: This is a linux system and Linuxthreads was not
found. On linux Linuxthreads should be used. So install Linuxthreads
(or a new glibc) and try again. See the Installation chapter in the
Reference Manual.
```
因为在早期的`glic`下需要安装`Linuxthreads`，以支持多线程功能。如今的系统几乎都有posix的`pthread`库了，不再需要Linuxthreads。要修改configure文件（当然也可以修改configure.in，然后autoconf重新生成configure),将

```text

with_posix_threads="no"
# Hack for DEC-UNIX (OSF1)
```
改为

```text

with_posix_threads="yes"
with_named_thread="-lpthread"
# Hack for DEC-UNIX (OSF1)
```

* 2、errno相关错误解决

在链接 `libmysql/my_malloc.o` 等文件时，会报`errno`相关错误。由于涉及的文件很多，粗暴的方式就是在`include/mysys_err.h`文化增加`#include "errno.h"`，同时在`libmysql/mysys_priv.h`中增加`#include "mysys_err.h"`即可。


* 3、max定义错误

修改`include/global.h`文件中，max和min宏定义。具体操作，就是将（注意副作用）

```text

/* Define som useful general macros */
if defined(__cplusplus) && defined(__GNUC__)
#define max(a, b)	((a) >? (b))
#define min(a, b)	((a) <? (b))
#elif !defined(max)

#define max(a, b)	((a) > (b) ? (a) : (b))
#define min(a, b)	((a) < (b) ? (a) : (b))
#endif
```
改为

```text

/* Define som useful general macros */
#undef max
#undef min
#define max(a, b)	((a) > (b) ? (a) : (b))
#define min(a, b)	((a) < (b) ? (a) : (b))
```

* 4、`MY_MUTEX_INIT_FAST`对应的`my_fast_mutexattr`未定义引用问题：这个需要定义`PTHREAD_ADAPTIVE_MUTEX_INITIALIZER_NP`宏

* 5、处理器构架相关的错误

你可能使用`-m32`去编译代码了，但最后可能还会报如下的错误：

```text

... i386 architecture of input file "xxx.o" is incompatible with i386:x86-64 output
```
因为用的是64位的`ld`，所以会出现此错误。可以通过参数`LDEMULATION=-elf32-x86-64`配置生成兼容32和64位的目标代码。

* 6、生成Makefile文件成功编译MySQL

因为默认不编译Innodb引擎（innobase目录），所以需要家`--with-innodb=yes`选项。最后，通过以下命令生成Makefile，并成功编译MySQL：

```text

$ CFLAGS="-m32 -DPTHREAD_ADAPTIVE_MUTEX_INITIALIZER_NP" CXXFLAGS=-m32 LDEMULATION=-elf32-x86-64 ./configure --with-innodb=yes --with-debug=yes 
```

以上就是我遇到的编译相关的主要问题。我的修改方式，比较简单粗暴，因为觉得针对老版本没有深究的必要（也没有必要搞patch。[微盘中有一个副本](http://vdisk.weibo.com/s/zvn9RXCUaV1mw)）。如果你有更好的处理方式，欢迎赐教。Ok，搞定了，可以继续研究别的内容了。


## 扩展阅读


## 祝大家玩的开心

## 编程之道，就在[编程之美]

![编程之美](/img/weixin_qr.jpg)




