---
layout: post
title: 用Sysbench 0.5测试postgresql
date: 2014-12-10
categories:
  - 技术
tags:
  - sysbench
---
## sysbench

[sysbench](https://launchpad.net/sysbench)是一个通用系统基准测试工具，支持针对MySQL、Oracle和PostgreSQL的性能测试。目前，主要支持的测试类型，如下：

* fileio - 文件 I/O测试
* cpu - CPU系能测试
* memory - 内存功能速度测试
* threads - 线程子系统系能测试
* mutex - 互斥性能测试
* oltp - 联机事务处理


sysbench是`Alexey Kopytov`在10多年前为MySQL基准测试编写的工具。官方发布的最新版是`Sysbench 0.4.12.5`，而`Sysbench 0.5`是主干的版本，还没有发布，不过可以通过以下的方式获取并编译：

```shell
 bzr branch lp:sysbench sysbench-trunk
 cd sysbench-trunk
 ./autogen.sh
 ./configure
 make

```

`Sysbench 0.5`带来的主要变化是，__内嵌了lua虚拟机，同时OLTP测试通过lua脚本的方式进行，取消了`oltp`参数和相关选项__（如`-test=oltp`）。自带的lua测试脚本位于`sysbench/tests/db`目录，可以作为参考。你可以编写自己的lua脚本，方便针对各种场景的oltp测试。例如，

```shell

 ./sysbench/sysbench --test=/usr/share/doc/sysbench/tests/db/insert.lua

```

另外，提供了`--num-threads`选项，便于指定测试线程数量，以及`--report-interval`选项，用于指定打印测试报告的时间间隔，及时得到结果反馈。


BTW，原来`http://sourceforge.net/projects/sysbench`已经不再维护，项目都被删除了。如果不想折腾`bzr`，可以下载我放到github的主干版本，其具体地址[https://github.com/pgresql/sysbench](https://github.com/pgresql/sysbench)。


## 扩展阅读

* [Using Lua-enabled sysbench](https://blog.mariadb.org/using-lua-enabled-sysbench/)
* [MySQL Benchmark Tool](http://dev.mysql.com/downloads/benchmarks.html)
* [Using sysbench 0.5 for performing MySQL benchmarks](http://www.percona.com/blog/2014/09/02/using-sysbench-0-5-benchmark-mysql-whats-changed-latest-release/)


## 祝大家玩的开心

## 编程之道，就在[编程之美]

![编程之美](/img/weixin_qr.jpg)

