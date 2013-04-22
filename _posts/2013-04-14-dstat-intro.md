---
layout: post
title: dstat,一块不错的主机性能采集工具
date: 2013-04-14
categories:
  - 技术
tags:
  - dstat
---
## dstat简介

[![](/img/article/2013-04/14-01.png)](http://dag.wieers.com/home-made/dstat)

很多人都习惯了用top/topas, vmstat等工具查看主机性能指标。这样做也不错，但太麻烦了，只能叹息如果有一个工具能方便统计、提取主机各方面的指标那就好了。[dstat](http://dag.wieers.com/home-made/dstat)就是针对Linux平台的类似工具，由python实现。尝试了一下，效果不错哦。

    Dstat is a versatile replacement for vmstat, iostat, netstat and ifstat. Dstat overcomes some of their limitations and adds some extra features, more counters and flexibility. Dstat is handy for monitoring systems during performance tuning tests, benchmarks or troubleshooting. 

    Dstat allows you to view all of your system resources in real-time, you can eg. compare disk utilization in combination with interrupts from your IDE controller, or compare the network bandwidth numbers directly with the disk throughput (in the same interval).


## 软件安装及使用

    sudo yum -y install dstat
    dstat


## 扩展阅读

* [Dstat: Versatile resource statistics tool](http://dag.wieers.com/home-made/dstat/)
* [dstat源码](https://github.com/dagwieers/dstat)
* [Linux Performance Analysis](http://web.eecs.utk.edu/~mucci/latest/pubs/LCSC2004.pdf)
* [Performance Analysis Methodology](http://www.brendangregg.com/Slides/LISA2012_methodologies.pdf)


## 祝大家玩的开心

