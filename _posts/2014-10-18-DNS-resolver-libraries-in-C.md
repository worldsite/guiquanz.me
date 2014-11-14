---
layout: post
title: C语言DNS请求分析库
date: 2014-10-18
categories:
  - 技术
tags:
  - DNS
---
## 引子

准备写一个基于C/C++的爬虫，需要一个`异步DNS`解析库，查了一些现有的库，总结如下：（一般使用`c-aresa`或`adns`就可以了）

* [c-aresa - a C library for asynchronous DNS requests](http://c-ares.haxx.se/)
* [ADNS - a GPL library which provides async DNS lookup](http://www.chiark.greenend.org.uk/~ian/adns/) __MIT__ 
* [UDNS - DNS Resolver Library](http://www.corpit.ru/mjt/udns.html#download) __LGPL__
* [FireDNS](http://directory.fsf.org/project/FireDNS/) __GPL__ (We asked the author about reconsidering the license. No luck.)
* [djbdns](http://cr.yp.to/djbdns.html) __Written by Dan Bernstein. The package is not allowed to be redistributed modified.__
* [Poslib](http://www.posadis.org/) __GPL. A DNS client/server library written in C++. Available for many platforms, including Linux, FreeBSD, other Unices and Windows.__
* [dns.c](http://25thandclement.com/~william/projects/dns.c.html) __MIT-style license__. A recursive, reentrant, non-blocking DNS resolver library in a single .c file.
* [dnspod-sr - 国内开源的一个DNS服务器，性能比BIND稍好](https://github.com/DNSPod/dnspod-sr)

## 扩展阅读


## 祝大家玩的开心

## 编程之道，就在[编程之美]

![编程之美](/img/weixin_qr.jpg)

