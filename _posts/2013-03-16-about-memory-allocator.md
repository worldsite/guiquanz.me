---
layout: post
title: 4个不错的内存管理库
date: 2013-03-16
categories:
  - 技术
tags:
  - Memory Allocator
---
## Memory Allocator

![](/img/article/2013-03/16-04.png)

The `TCMalloc` Memory Allocator
====================

    TCMalloc is faster than the glibc 2.3 malloc (available as a separate library called ptmalloc2) and other mallocs that I have tested. ptmalloc2 takes approximately 300 nanoseconds to execute a malloc/free pair on a 2.8 GHz P4 (for small objects). The TCMalloc implementation takes approximately 50 nanoseconds for the same operation pair. Speed is important for a malloc implementation because if malloc is not fast enough, application writers are inclined to write their own custom free lists on top of malloc. This can lead to extra complexity, and more memory usage unless the application writer is very careful to appropriately size the free lists and scavenge idle objects out of the free list.

TCMalloc also reduces lock contention for multi-threaded programs. For small objects, there is virtually zero contention. For large objects, TCMalloc tries to use fine grained and efficient spinlocks. ptmalloc2 also reduces lock contention by using per-thread arenas but there is a big problem with ptmalloc2's use of per-thread arenas. In ptmalloc2 memory can never move from one arena to another. This can lead to huge amounts of wasted space. For example, in one Google application, the first phase would allocate approximately 300MB of memory for its URL canonicalization data structures. When the first phase finished, a second phase would be started in the same address space. If this second phase was assigned a different arena than the one used by the first phase, this phase would not reuse any of the memory left after the first phase and would add another 300MB to the address space. Similar memory blowup problems were also noticed in other applications.

Another benefit of TCMalloc is space-efficient representation of small objects. For example, N 8-byte objects can be allocated while using space approximately 8N * 1.01 bytes. I.e., a one-percent space overhead. ptmalloc2 uses a four-byte header for each object and (I think) rounds up the size to a multiple of 8 bytes and ends up using 16N bytes.


The `Jemalloc` Memory Allocator
====================

jemalloc is a general-purpose scalable concurrent malloc(3) implementation. There are several divergent versions of jemalloc in active use, including:

* The canonical jemalloc distribution available via this website, which currently targets Linux, FreeBSD, Mac OS X, and Microsoft Windows.

* FreeBSD's default system allocator. This was the first public use of jemalloc, and it is still author-maintained.

* NetBSD's default system allocator (jemalloc.c).

* Mozilla Firefox's allocator ([source code](http://hg.mozilla.org/mozilla-central/file/tip/memory/jemalloc)), specifically for Microsoft Windows-related platforms, Solaris, and Linux. There is Apple Mac OS X support code as well, but it has yet to be used in a release.

![](/img/article/2013-03/16-06.jpg)


The `Lockless` Memory Allocator
====================

The Lockless Memory Allocator is downloadable under the GPL 3.0 License.

    Multithread Optimized
    The Lockless memory allocator uses lock-free techniques to minimize latency and memory contention. This provides optimal scalability as the number of threads in your application increases. Per-thread data is used to reduce bus communication overhead. This results in thread-local allocations and frees not requiring any synchronization overhead in most cases.


The `Hoard` Memory Allocator
====================
	
    The Hoard memory allocator is a fast, scalable, and memory-efficient memory allocator for Linux, Solaris, Mac OS X, and Windows. Hoard is a drop-in replacement for malloc that can dramatically improve application performance, especially for multithreaded programs running on multiprocessors and multicore CPUs. No source code changes necessary: just link it in or set one environment variable (see [Using Hoard](http://plasma.cs.umass.edu/emery/index.php?page=using-hoard)). 

Source code is now on `GitHub`	[https://github.com/emeryberger/Hoard](https://github.com/emeryberger/Hoard)


## 扩展阅读

* [Benchmarks of the Lockless Memory Allocator](http://locklessinc.com/benchmarks_allocator.shtml)
* [A Scalable Concurrent malloc(3) Implementation for FreeBSD](http://people.freebsd.org/~jasone/jemalloc/bsdcan2006/jemalloc.pdf)
* [TCMalloc : Thread-Caching Malloc](http://google-perftools.googlecode.com/svn/trunk/doc/tcmalloc.html)
* [优化的内存访问 TCMalloc](http://my.oschina.net/captaintheron/blog/2797)
* [Lockless Memory Allocator试用记](http://blog.yufeng.info/archives/tag/%E5%86%85%E5%AD%98%E5%88%86%E9%85%8D%E5%99%A8)
* [更好的内存管理-jemalloc](http://wangkaisino.blog.163.com/blog/static/1870444202011431112323846/)
* [TCMalloc：线程缓存的Malloc](http://shiningray.cn/tcmalloc-thread-caching-malloc.html)

