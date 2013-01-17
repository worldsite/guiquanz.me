--- 
layout: post
title: Likwid——一块不错的Linux高性能软件开发辅助工具
date: 2013-01-16
categories:
  - 技术
tags:
  - Likwid
---

## Likwid简介

![](/img/article/likwid.jpg)

Likwid（Like I knew what I am doing的缩写），是一块针对Linux平台`高性能多线程应用开发`的`开源`辅助工具,`德国造`。便于开发人员更好的了解系统的`CPU拓扑结构`，`内存使用情况`，各种`CPU性能计数器的数字`，各种`CPU Cache的使用情况`，`命中率`等信息，准确的分析出程序的缺陷，找到更好的优化点。

`Likwid`的项目地址[http://code.google.com/p/likwid](http://code.google.com/p/likwid/)。目前已经发展到3.0版本了，主要亮点，包括：

    1. New application likwid-memsweeper to cleanup ccNUMA memory domains. This functionality is also integrated in likwid-pin.
    2. Support for Intel Sandy Bridge Uncore (partial)
    3. Support for Intel Ivy Bridge (only core part)
    4. Initial support for Intel Xeon Phi (KNC)
    5. Better support for AMD Interlagos
    6. The OpenMP type is now detected automatically while pinning
    7. Lots of bugfixes and improvements
    8. Marker API works now for threaded code and accessDaemon
    9. Convenient macro wrapper for Marker API
    10. Data volume as new metric in all memory/cache groups
    11. Updated Wiki documentation

Likwid主要组件，如下：

`likwid-topology`: Show the thread and cache topology

`likwid-perfctr`: Measure hardware performance counters on Intel and AMD processors

`likwid-features`: Show and Toggle hardware prefetch control bits on Intel Core 2 processors

`likwid-pin`: Pin your threaded application without touching your code (supports pthreads, Intel OpenMP and gcc OpenMP)

`likwid-bench`: Benchmarking framework allowing rapid prototyping of threaded assembly kernels

`likwid-mpirun`: Script enabling simple and flexible pinning of MPI and MPI/threaded hybrid applications

`likwid-perfscope`: Frontend for likwid-perfctr timeline mode. Allows live plotting of performance metrics.

`likwid-powermeter`: Tool for accessing RAPL counters and query Turbo mode steps on Intel processor.

`likwid-memsweeper`: Tool to cleanup ccNUMA memory domain


## Likwid安装及使用

**此操作，仅针对 Fedora 17平台**

Likwid不依赖于Linux内核，支持`2.6以及更高的Linux版本`，安装过程也很简单。

1、软件安装

<pre class="prettyprint linenums">
cd ~
hg clone http://code.google.com/p/likwid/
cd likwid && make && sudo make install
</pre>

2、尝试：查看拓扑信息

<pre class="prettyprint">
$ sudo ./likwid-topology

-------------------------------------------------------------
CPU type:	Intel Core 2 45nm processor 
*************************************************************
Hardware Thread Topology
*************************************************************
Sockets:	1 
Cores per socket:	2 
Threads per core:	1 
-------------------------------------------------------------
HWThread	Thread		Core		Socket
0		0		0		0
1		0		1		0
-------------------------------------------------------------
Socket 0: ( 0 1 )
-------------------------------------------------------------

*************************************************************
Cache Topology
*************************************************************
Level:	1
Size:	32 kB
Cache groups:	( 0 ) ( 1 )
-------------------------------------------------------------
Level:	2
Size:	3 MB
Cache groups:	( 0 1 )
-------------------------------------------------------------

*************************************************************
NUMA Topology
*************************************************************
NUMA domains: 1 
-------------------------------------------------------------
Domain 0:
Processors:  0 1
Relative distance to nodes:  10
Memory: 134.742 MB free of total 1941.96 MB
-------------------------------------------------------------
</pre>

3、分析参考

![](/img/article/likwid_demo.png)


## 扩展阅读

1. [LIKWID: Lightweight performance tools](http://tools.zih.tu-dresden.de/2011/downloads/treibig-likwid-ParTools.pdf)



