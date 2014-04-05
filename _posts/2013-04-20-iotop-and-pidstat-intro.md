---
layout: post
title: iotop和pidstat，查看线程IO的利器
date: 2013-04-20
categories:
  - 技术
tags:
  - iotop
  - pidstat
---

iotop和pidstat，可以辅助观察、分析线程的IO情况。iotop呈现的是扁平化的线程IO，而pidstat呈现的则是层次化的。

iotop示例：
![](/img/article/2013-04/20-02.png)

pidstat示例：

![](/img/article/2013-04/20-01.png)


## 软件安装及使用

<pre class="prettyprint linenums">
    sudo yum -y install iotop
    sudo iotop

    sudo yum -y install pidstat
    pidstat
</pre>

**前提**： 仅适用于 Linux 6.2.23之后的内核版本。


## 扩展阅读

* [Iotop 主页](http://guichaz.free.fr/iotop/)
* [git clone git://repo.or.cz/iotop.git](git clone git://repo.or.cz/iotop.git)


## 祝大家玩的开心

