---
layout: post
title: 解决dbaccess不识别终端错误
date: 2014-10-10
categories:
  - 技术
tags:
  - informix
---
## 问题描述

INFOEMIX仍然是金融和电信行业最主流的数据库产品，如果你是这些行业的从业者，那么经常要和INFORMIX打交道了。其中dbaccess是远程访问数据库的一个终端操作工具，有时你可能会碰上"The type of your terminal is unknown to the system"的问题。具体如下，当在终端上键入dbaccess命令时，程序提示“本系统不识别你的终端类型”。此时你需要在`~/.profile`文件中配置你的终端类型，然后执行`. ~/.profile`让配置生效即可。

##（1）问题场景

```bash

geek@world> dbaccess
 

The type of your terminal is unknown to the system

```

##（2）解决方案

```bash

geek@world> vi ~/.profile

".profile" 54 lines, 1236 characters 

...

########################### TERM ######################################

TERM=vt100

TERMCAP=$INFORMIXDIR/etc/termcap

export TERM TERMCAP
```

##（3）让命令生效

```bash

geek@world> . ~/.profile
```


## 扩展阅读


## 祝大家玩的开心

## 编程之道，就在[编程之美]

![编程之美](/img/weixin_qr.jpg)

