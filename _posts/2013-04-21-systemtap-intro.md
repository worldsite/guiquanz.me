---
layout: post
title: SystemTap，万能的性能测量和调式诊断工具
date: 2013-04-21
categories:
  - 技术
tags:
  - SystemTap
---
## SystemTap

[![](/img/article/2013-04/21-01.jpg)](http://sourceware.org/systemtap/index.html)

    SystemTap provides free software (GPL) infrastructure to simplify the gathering of information about the running Linux system. This assists diagnosis of a performance or functional problem. SystemTap eliminates the need for the developer to go through the tedious and disruptive instrument, recompile, install, and reboot sequence that may be otherwise required to collect data.

    SystemTap provides a simple command line interface and scripting language for writing instrumentation for a live running kernel plus user-space applications. We are publishing samples, as well as enlarging the internal "tapset" script library to aid reuse and abstraction.

    Among other tracing/probing tools, SystemTap is the tool of choice for complex tasks that may require live analysis, programmable on-line response, and whole-system symbolic access. SystemTap can also handle simple tracing jobs.

    Current project members include Red Hat, IBM, Hitachi, and Oracle.

SystemTap的数据流图:

![](/img/article/2013-04/21-02.jpg)

SystemTap的实现原理：

![](/img/article/2013-04/21-03.gif)


## 软件安装及使用
    
    yum install systemtap kernel-devel
    debuginfo-install kernel

或者，先安装systemtap，然后执行stap-prep确认需要安装的其他组建：
    
    yum install systemtap
    stap-prep


### 简单应用示例
    
    sudo stap -ve 'probe begin { log("hello world") exit() }'
    

## 扩展阅读

* agentzh，针对Nginx-Lua调试的工具集[nginx-systemtap-toolkit](https://github.com/agentzh/nginx-systemtap-toolkit)
* [Introduction to SystemTap A pratical approach](http://raisama.net/talks/fisl10/kernel-hacking/stap.pdf)
* [SystemTap: Instrumenting the Linux Kernelfor Analyzing Performance and Functional Problems](http://www.redbooks.ibm.com/redpapers/pdfs/redp4469.pdf)
* [Linux Performance and Tuning Guidelines](http://www.redbooks.ibm.com/redpapers/pdfs/redp4285.pdf)
* [systemtap主页](http://sourceware.org/systemtap/index.html)
* [SystemTap Tapset Reference For SystemTap in Red Hat Enterprise Linux 6]()
* [SystemTap/DTrace with MySQL & Drizzle](http://cdn.oreillystatic.com/en/assets/1/event/36/Monitoring%20Drizzle%20or%20MySQL%20With%20DTrace%20and%20SystemTap%20Presentation.pdf)
* [Various SystemTap scripts for MySQL and Drizzle](http://github.com/posulliv/stap)
* [SystemTap Wiki](http://sourceware.org/systemtap/wiki/HomePage)

## 祝大家玩的开心

