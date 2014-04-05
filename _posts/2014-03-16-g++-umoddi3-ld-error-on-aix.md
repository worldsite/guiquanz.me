---
layout: post
title: AIX平台G++编译未定义umoddi3错误解决方案
date: 2014-03-16
categories:
  - 技术
tags:
  - g++
---

在AIX平台，`g++`编译连接时，可能会报”.__umoddi3“未定义错误。具体错误，如下：

ld: 0711-224 WARNING: Duplicate symbol: .__umoddi3
ld: 0711-224 WARNING: Duplicate symbol: .__udivdi3
ld: 0711-224 WARNING: Duplicate symbol: .__divdi3
ld: 0711-224 WARNING: Duplicate symbol: .__moddi3
ld: 0711-224 WARNING: Duplicate symbol: .__udivmoddi4
ld: 0711-345 Use the -bloadmap or -bnoquiet option to obtain more information. 

最奇葩的解决方案是，增加连接选项：`-lgcc`


## 用`xlC`编译时，如果报告”libiconv_open“未定义，该如何解决？

ld: 0711-317 ERROR: Undefined symbol: .libiconv_open
ld: 0711-317 ERROR: Undefined symbol: .libiconv
ld: 0711-317 ERROR: Undefined symbol: .libiconv_close
collect2: ld returned 8 exit status
make: The error code from the last command is 1.

首先，执行`lslpp -l |grep iconv`，查看libiconv是否已经被安装了。

i@home:/> `lslpp -l |grep iconv`
  bos.iconv.com              5.3.9.0  COMMITTED  Common Language to Language
  bos.iconv.ucs.com          5.3.9.0  COMMITTED  Unicode Base Converters for
  bos.rte.iconv              5.3.8.0  COMMITTED  Language Converters

（安装了3个，通过-L指定一个适合的路径即可。可以配合使用locale、whereis等确定路径）

如果，没有安装。可以到[AIX Open Source Packages](http://www.perzl.org/aix/)网站，
搜索并下载对应的rpm包，然后安装即可。

（BTW: 针对 HO-UX 平台，开源软件移植中心为:
[Welcome to the HP-UX Porting and Archive Centre](http://hpux.connect.org.uk/)）


## 扩展阅读


## 祝大家玩的开心

## 编程之道，就在[编程之美]

![编程之美](/img/weixin_qr.jpg)

