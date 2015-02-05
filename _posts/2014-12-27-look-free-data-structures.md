---
layout: post
title: 几个不错的C/C++ Lock-Free/STM库
date: 2014-12-27
categories:
  - 技术
tags:
  - Lock-Free
---
## 日渐升温的Lock-Free和STM

最早了解`Lock-Free`，应该是很多年前，研究Cambridge大学[Practical lock-free data structures](http://www.cl.cam.ac.uk/research/srg/netos/lock-free/)系列文章及相关的[lockfree-lib](https://github.com/guiquanz/lockfree)实现时，后来忙于工作，无暇关注。现在随着多核技术的普及`lock-free`又开始慢慢升温了。因为实现复杂，正确性、可靠性等方面很难完备的证明，但这不妨碍其发展，已经有很多人在尝试了：

* [Mintomic- an API for low-level lock-free programming in C and C++](http://mintomic.github.io/)
* [The Concurrency Kit](http://concurrencykit.org/index.html)
* [liblfds - a portable, license-free, lock-free data structure library written in C](http://www.liblfds.org/)
* [TinySTM - a lightweight and efficient word-based STM implementation](https://github.com/patrickmarlier/tinystm)
* [atomic_ops - Atomic memory update operations portable implementation](https://github.com/ivmai/libatomic_ops)
* [PyPy libstmgc](https://bitbucket.org/pypy/stmgc/src/10c636ae449e?at=default#)


以上是一些C/C++无锁库，在此存照，以备不时之需。


## 扩展阅读

* [Mintomic (short for “minimal atomic”) is an API for low-level lock-free programming in C and C++](http://mintomic.github.io/)
* [Introducing Mintomic: A Small, Portable Lock-Free API](http://preshing.com/20130505/introducing-mintomic-a-small-portable-lock-free-api/)
* [The Concurrency Kit](http://concurrencykit.org/index.html)
* [Nonblocking Algorithms and Scalable Multicore Programming](http://queue.acm.org/detail.cfm?id=2492433)
* [liblfds, a portable, license-free, lock-free data structure library written in C](http://www.liblfds.org/)
* [Introduction to Lock-Free Algorithms](http://concurrencykit.org/presentations/lf1/lf1.pdf)
* [Lock-free algorithm library](http://stackoverflow.com/questions/6572714/lock-free-algorithm-library)
* [Lock-free Cuckoo Hashing](http://www.cse.chalmers.se/~tsigas/papers/ICDCS14.pdf)
* [Concurrent Data Structures](http://www.cs.tau.ac.il/~shanir/concurrent-data-structures.pdf)
* [Lock-based Concurrent Data Structures](http://pages.cs.wisc.edu/~remzi/OSTEP/threads-locks-usage.pdf)
* [libphenom - An eventing framework for building high performance and high scalability systems in C](https://github.com/facebook/libphenom)
* [PyPy - Software Transactional Memory](http://pypy.readthedocs.org/en/latest/stm.html)
* [Getting Started with tinySTM](http://kris.kalish.net/2009/09/getting-started-with-tinystm-ubuntu-9-04/)
* [fastSTM - a userspace library for software transactional memory](https://nebelwelt.net/publications/students/08hs-saurer-libstm.pdf)
* [Time-based Software Transactional Memory](http://doi.ieeecomputersociety.org/10.1109/TPDS.2010.49)
* [Dynamic Performance Tuning of Word-Based Software Transactional Memory](http://se.inf.tu-dresden.de/pubs/papers/felber2008tinystm.pdf)
* [Making Object-Based STM - Practical in Unmanaged Environments](http://wwwa.unine.ch/transact08/slides/riegel-making.pdf)
* [TinySTM - a lightweight and efficient word-based STM implementation](http://tmware.org/tinystm)


## 祝大家玩的开心

## 编程之道，就在[编程之美]

![编程之美](/img/weixin_qr.jpg)

