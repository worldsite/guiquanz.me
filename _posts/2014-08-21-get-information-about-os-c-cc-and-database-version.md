---
layout: post
title: 主机、C/C++编译器和数据库类型及版本信息提取
date: 2014-08-21
categories:
  - 技术
tags:
  - tools
---

__获取配置信息，务必[对号入座]__

## 主机操作系统类型及版本

```bash

ruby@home> uname -a
```

## 主机C/C++编译器版本

2.1 LINUX平台

```bash

ruby@home> g++ --version
g++ (GCC) 4.1.2 20080704 (Red Hat 4.1.2-48)
Copyright (C) 2006 Free Software Foundation, Inc.
This is free software; see the source for copying conditions.  There is NO
warranty; not even for MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.

ruby@home> gcc --version
gcc (GCC) 4.1.2 20080704 (Red Hat 4.1.2-48)
Copyright (C) 2006 Free Software Foundation, Inc.
This is free software; see the source for copying conditions.  There is NO
warranty; not even for MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE
```

2.2 AIX平台

```bash

ruby@home> lslpp -l|grep xlC
  xlC.adt.include            9.0.0.0  COMMITTED  C Set ++ Application
  xlC.aix50.rte             10.1.0.0  COMMITTED  XL C/C++ Runtime for AIX 5.3
  xlC.cpp                    9.0.0.0  COMMITTED  C for AIX Preprocessor
  xlC.msg.en_US.cpp          9.0.0.0  COMMITTED  C for AIX Preprocessor
  xlC.msg.en_US.rte         10.1.0.0  COMMITTED  XL C/C++ Runtime
  xlC.rte                   10.1.0.0  COMMITTED  XL C/C++ Runtime

ruby@home> xlc -qversion
IBM XL C/C++ Enterprise Edition for AIX, V9.0
Version: 09.00.0000.0000
```

2.3 HP-UX 9000/800平台

```bash

ruby@home> aCC -V
aCC: HP ANSI C++ B3910B A.03.65
```

2.4 HP-UX ia64平台

```bash

ruby@home> aCC -V
aCC: HP C/aC++ B3910B A.06.12 [Nov 03 2006]
```

## 数据库类型及版本

3.1 INFORMIX环境

```bash

ruby@home> onstat -V
IBM Informix Dynamic Server Version 10.00.FC6 -- On-Line -- Up 76 days 00:16:38 -- 4211284 Kbytes
```

3.2 ORACLE环境

```bash

ruby@home> sqlplus sms/sms@oracle1   //通过sqlplus接入库
SQL> select * from v$version;

BANNER
----------------------------------------------------------------
Oracle Database 10g Enterprise Edition Release 10.2.0.1.0 - 64bi
PL/SQL Release 10.2.0.1.0 - Production
CORE    10.2.0.1.0      Production
TNS for IBM/AIX RISC System/6000: Version 10.2.0.1.0 - Productio
NLSRTL Version 10.2.0.1.0 – Production
或者
ruby@home> sqlplus -V

SQL*Plus: Release 10.2.0.1.0 - Production
```

3.3 PostgreSQL环境

```bash

ruby@home> psql --version
psql (PostgreSQL) 9.4beta2
```


## 扩展阅读


## 祝大家玩的开心

## 编程之道，就在[编程之美]

![编程之美](/img/weixin_qr.jpg)

