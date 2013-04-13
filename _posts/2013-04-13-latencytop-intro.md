---
layout: post
title: latencytop,一块不错的系统延时检测工具
date: 2013-04-13
categories:
  - 技术
tags:
  - latencytop
---
## latencytop简介

![](/img/article/2013-04/13-02.png)

    `latencytop` is a Linux* tool for software developers (both kernel and userspace), aimed at identifying where in the system latency is happening, and what kind of operation/action is causing the latency to happen so that the code can be changed to avoid the worst latency hiccups.

    LatencyTOP focuses on the cases where the applications want to run and execute useful code, but there's some resource that's not currently available (and the kernel then blocks the process). This is done both on a system level and on a per process level, so that you can see what's happening to the system, and which process is suffering and/or causing the delays.

    You can walk the processes by using the cursor keys. If you press s followed by a letter, then only active processes starting with that lettter are displayed and walked. If you press s followed by 0 then that filter is reset.

    If you press f then LatencyTop displays a list of all processes currently waiting for an fsync to finish. Pressing f again returns you to the normal operating mode of LatencyTop。


## 软件安装及使用

    sudo yum install -y latencytop
    sudo latencytop


## 扩展阅读

* [latencytop - a tool for developers to visualize system latencies](http://linux.die.net/man/8/latencytop)
* [Latency Numbers Every Programmer Should Know](http://www.eecs.berkeley.edu/~rcs/research/interactive_latency.html)
* [latencytop深度了解你的Linux系统的延迟](http://blog.yufeng.info/archives/tag/dstat)


## 祝大家玩的开心

