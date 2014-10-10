---
layout: post
title: 解决因Dwarf版本不匹配，造成gdb无法加载调试信息错误
date: 2014-10-06
categories:
  - 技术
tags:
  - Dwarf
  - gdb
---
## 缘由

当程序跑飞掉的时候，如果能令其生成core文件那该多好。但如果用gdb调试core时，没有调试信息呢（此处说的是，编译时加了`-g`选项,没有使用`-O0`以上的编译优化，也没有对可执行文件执行`strip`去掉符号信息）？我就碰见上了这个场景。具体的信息，如下：

```text

warning: Can't read pathname for load map: Input/output error.
Reading symbols from /usr/local/lib/xxx.so.2...Error while reading shared library symbols:
Dwarf Error: wrong version in compilation unit header (is 4, should be 2) [in module /usr/local/lib/xxx.so.2]
Reading symbols from /usr/lib64/libdl.so.2...done.
Loaded symbols for /usr/lib64/libdl.so.2
Reading symbols from /usr/lib64/libstdc++.so.6...done.
Loaded symbols for /usr/lib64/libstdc++.so.6
Reading symbols from /usr/lib64/libm.so.6...BFD: /usr/lib64/libm.so.6: invalid relocation type 37
BFD: BFD (GNU Binutils) 2.18.50.20080226 assertion fail elf64-x86-64.c:278
BFD: /usr/lib64/libm.so.6: invalid relocation type 37
BFD: BFD (GNU Binutils) 2.18.50.20080226 assertion fail elf64-x86-64.c:278
BFD: /usr/lib64/libm.so.6: invalid relocation type 37
...
BFD: /usr/lib64/libc.so.6: invalid relocation type 37
BFD: BFD (GNU Binutils) 2.18.50.20080226 assertion fail elf64-x86-64.c:278
done.
Loaded symbols for /usr/lib64/libc.so.6
Reading symbols from /usr/lib64/libz.so.1...done.
Loaded symbols for /usr/lib64/libz.so.1
Reading symbols from /usr/lib64/ld-linux-x86-64.so.2...done.
Loaded symbols for /lib64/ld-linux-x86-64.so.2
Reading symbols from /usr/local/lib/xxx.so.2...Error while reading shared library symbols:
Dwarf Error: wrong version in compilation unit header (is 4, should be 2) [in module /usr/local/lib/xxx.so.2]

warning: no loadable sections found in added symbol-file system-supplied DSO at 0x7fffacc68000

```

由提示信息可知，因为gdb使用的Dwarf是2,而gcc/g++使用的Dwarf是4,版本不匹配，造成调试符号信息无法加载。最后，查了一下我的`gdb是6.8.0`的版本，而`gcc/g++ 是4.8.2`的版本。发现从[gcc/g++ 4.5](https://gcc.gnu.org/gcc-4.5/changes.html)`版本开始依赖很多Dwarf 3和Dwarf 4的特性，但升级GCC时，没有升级gdb所致。具体解决方案有：

* 第一种： 升级gdb

* 第二种： 让gcc/g++生存Dwarf2版本的调试信息： 增加gcc/g++编译选项 `-gdwarf-2 -gstrict-dwarf`


## 扩展阅读

* [GCC 4.5 Release Series - Changes, New Features, and Fixes](https://gcc.gnu.org/gcc-4.5/changes.html)


## 祝大家玩的开心

## 编程之道，就在[编程之美]

![编程之美](/img/weixin_qr.jpg)

