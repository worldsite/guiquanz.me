---
layout: post
title: TokuDB背后的技术团队
date: 2014-08-23
categories:
  - 技术
tags:
  - TokuDB
---
## TokuDB八卦

[Tokudb](https://github.com/Tokutek/tokudb-engine)是一个针对`MySQL`和`MariaDB`的非常知名的插件式存储引擎。自从被[tokutek](http://www.tokutek.com/)公司开源（`包括所有的变更记录`）之后，一直备受开源社区的青睐。其背后的团队是怎么个NB呢？

* 3位创始人: Michael A. Bender, Martín Farach-Colton, Bradley C. Kuszmaul 

他们于2012年发表了一篇208页的[Data Structures and Algorithms for Big Databases](http://www.tokutek.com/wp-content/uploads/2012/09/BenderKuszmaul-tutorial-xldb12.pdf)文档，曾轰动一时。

目前TokuDB有５名研发:

```text

 @prohaska    --tokudb-engine研发，版本发布(一个人)
 @Leif        --tokuFT研发(Bender的学生)
 @zkasheff    --tokuFT研发(Kuszmaul的学生)
 @esmet       --tokuFT研发(Farach的学生)
 @fizzfaldt   --算法优化(Bender的学生)
```

可以说这是个非常“精致”的团队，是研发也是测试。[tokuFT](https://github.com/Tokutek/ft-index)的测试代码达`~18w`行(核心代码才`~9w`行)，在代码把控很严格，要求所有代码在valgrind(helgrind和drd)下，没有`memory`、`data race`和`lock order`警告，质量很有保障。

同时@Leif和@zkasheff也是tokuMX的研发，不久前，他俩就MongoDB Replication可能＂丢数据＂的问题，写了篇[Ark: A Real-World Consensus Implementation](http://arxiv.org/pdf/1407.4765v1)，对[Paxos](http://research.google.com/archive/paxos_made_live.html)和[The Raft Consensus Algorithm](http://raft-consensus.github.io/)感兴趣的同学可以去看下。


## 扩展阅读

* [A Comparison of Log-Structured Merge (LSM) and Fractal Tree Indexing](http://forms.tokutek.com/acton/attachment/6118/f-0039/1/-/-/-/-/lsm-vs-fractal.pdf)
* [Ark: A Real-World Consensus Implementation](http://arxiv.org/pdf/1407.4765v1)
* [Introducing Ark: A Consensus Algorithm For TokuMX and MongoDB](http://www.tokutek.com/2014/07/introducing-ark-a-consensus-algorithm-for-tokumx-and-mongodb/)
* [Explaining Ark Part 1: The Basics](http://www.tokutek.com/2014/07/explaining-ark-part-1-the-basics/)
* [Explaining Ark Part 2: How Elections and Failover Currently Work](http://www.tokutek.com/2014/07/explaining-ark-part-2-how-elections-and-failover-currently-work/)
* [Explaining Ark Part 3: Why Data May Be Lost on a Failover](http://www.tokutek.com/2014/07/explaining-ark-part-3-why-data-may-be-lost-on-a-failover/)
* [Explaining Ark Part 4: Fixing Majority Write Concern](http://www.tokutek.com/2014/08/explaining-ark-part-4-fixing-majority-write-concern/)
* [Data Structures and Algorithms for Big Databases](http://www.tokutek.com/wp-content/uploads/2012/09/BenderKuszmaul-tutorial-xldb12.pdf)
* [The Raft Consensus Algorithm](http://raft-consensus.github.io/)
* [Raft user study](https://ramcloud.stanford.edu/~ongaro/userstudy/)
* [Paxos Simple](http://research.microsoft.com/en-us/um/people/lamport/pubs/pubs.html#paxos-simple)
* [Paxos Made Live](http://research.google.com/archive/paxos_made_live.html)
* [Fast Paxos](http://research.microsoft.com/apps/pubs/default.aspx?id=64624)
* [TokuFT is a high-performance, transactional key-value store](https://github.com/Tokutek/ft-index)
* [Paxos for System Builders](http://www.dsn.jhu.edu/Paxos-SB.html)
* [Paxos For System Builders: An Overview](http://www.cnds.jhu.edu/pub/papers/psb_ladis_08.pdf)
* [Google Whitepaper: Chubby Distributed Lock Service](http://research.google.com/archive/chubby.html)


## 祝大家玩的开心

## 编程之道，就在[编程之美]

![编程之美](/img/weixin_qr.jpg)

