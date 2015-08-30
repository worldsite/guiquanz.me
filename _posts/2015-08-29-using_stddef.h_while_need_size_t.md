---
layout: post
title: 需要 size_t 时，请使用 stddef.h 文件
date: 2015-08-29
categories:
  - 技术
tags:
  - size_t
---
## 需要 size_t 时，请使用 stddef.h 文件

在看 `NAXSI` 中 libinjection 代码时，经常会看到如下的代码：


```c
/* pull in size_t */

#include <string.h>

```

头文件中需要 size_t ，却 include 了 string.h 文件。这样做诚然没有错，因为 string.h 中需要 size_t ，所以不管平台如何定义 size_t 了，string.h 中一定会引入相关声明,但 从 C89 开始就有了 stddef.h 定义 size_t。鉴于 API 最小化依赖及大家之间的共识，强烈建议使用 stddef.h 文件。


我们可以看一下 [OpenGroup](http://www.unix.org/version4/)2013[规范中 string.h 的定义](http://pubs.opengroup.org/onlinepubs/7908799/xsh/string.h.html)，如下：

```c
#include <string.h>

DESCRIPTION

The <string.h> header defines the following:
   NULL
Null pointer constant.
  size_t
  As described in <stddef.h>.

The following are declared as functions and may also be defined as macros. Function prototypes must be provided for use with an ISO C compiler.

void *memccpy(void *, const void *, int, size_t);
void *memchr(const void *, int, size_t);
int memcmp(const void *, const void *, size_t);
...
```

* Inclusion of the <string.h> header may also make visible all symbols from <stddef.h>.


## 可移植性，不能成为不规范的借口

这些年做过非常多跨平台的 C/C++ 软件开发，遇见过各种奇葩的问题，但原则性的内容，不应该作为可移植性的借口。我们看一下仅支持 C89 标准的 HP-UX 主机中 size_t 的声明情况：

```bash

i@home> cat /usr/include/string.h |grep include
# include <sys/stdsyms.h>
# include <sys/_inttypes.h>
# include <sys/_null.h>
** Can not include <sys/types.h> here because
# include <sys/_size_t.h>
```

```bash

i@home> cat /usr/include/stddef.h |grep include
# include <sys/stdsyms.h>
*# include <sys/_inttypes.h>
# include <sys/_null.h>
# include <sys/_size_t.h>
# include <sys/_wchar_t.h>
```


```bash

i@home> cat /usr/include/sys/_size_t.h |grep size_t
/* @(#) $Revision: ../hdr/sys/_size_t.h@@/main/i80/2 $ */
** File: <sys/_size_t.h>
** the size_t type. This file should be included
** by all other header files if they need size_t
** for determining if size_t can be included
typedef unsigned long size_t;
```

```bash

i@home> cat /usr/include/string.h |grep size_t
# include <sys/_size_t.h>
extern int memcmp(const void *, const void *, size_t);
extern char *strncat(char *, const char *, size_t);
...
extern size_t strcspn();
extern size_t strspn();
extern size_t strlen();
extern void *memccpy(void *, const void *, int, __size_t);
```

```bash

i@home> cat /usr/include/stddef.h |grep size_t 
# define offsetof(__s_name, __m_name) ((__size_t)&(((__s_name*)0)->__m_name))
# include <sys/_size_t.h>
```

从以上分析不难发现，size_t 是在 `sys/_size_t.h` 中定义的 `typedef unsigned long size_t`，而 `stddef.h` 和 `string.h` 中都有引入处理。如果你从事的是特种设备开发（如特殊的嵌入式系统，各种实现都不满足相关规范），否则建议遵循规范。

``` bash

Name                     Header         Standard

size_t                   stddef.h       C89
size_t                   stdio.h        C89
size_t                   stdlib.h       C89
size_t                   string.h       C89
size_t                   time.h         C89
size_t                   uchar.h        C11
size_t                   wchar.h        C89
```

可进一步阅读[ISO C Names and corresponding headers](http://www.schweikhardt.net/identifiers.html) 和 [http://en.cppreference.com/w/c/types/size_t](http://en.cppreference.com/w/c/types/size_t)


如果你对`跨平台的 C/C++ 开发`非常关注，建议参考：（也不要错过各类知名跨平台的开源项目）

* The Definitive Guide to GCC（William von Hagen著）：全面介绍GCC相关内容，包括auto*工具链使用及语言特性的扩展等。
* Unix to Linux Porting (Alfredo mendoza等著)：这是*nix软件移植开发最好的指南。


## 扩展阅读


## 祝大家玩的开心

## 编程之道，就在[编程之美]

![编程之美](/img/weixin_qr.jpg)

