---
layout: post
title: dropwatch，一个检查网络协议栈丢包的利器
date: 2013-02-28
categories:
  - 技术
tags:
  - dropwatch
---
## 主题简介

What is [Dropwatch](https://fedorahosted.org/dropwatch)

Dropwatch is a project I am tinkering with to improve the visibility developers and sysadmins have into the Linux networking stack. Specifically I am aiming to improve our ability to detect and understand packets that get dropped within the stack. I've spent some time talking with many people about what they see as shorcommings in this area, and have come away with 4 points:

    Consolidation: Finding dropped packets in the network stack is currently very fragmented. There are numerous statistics proc files and other utilities that need to be consulted in order to have a full view of what packets are getting dropped within the stack. Consolidating all these utilities into one place is very helpful

    Clarity: Understanding which statistics and utility outputs correlate to actual dropped packets requres a good deal of knoweldge. Being able to simplify the ability to recognize a dropped packet is helpful

    Disambiguation: There is a gap between the recognition of a dropped packet and its root cause. Several statistics can be incremented at multiple points in the kernel, and sometimes for multiple reasons. Being able to point out, with specificity where and why a packet was dropped decreases the time it takes for a admin or developer to correct the problem.

    Performance: Checking the current user space utilities and stats for dropped packets is currently an exercise in polling. Its performance is sub-optimal and makes sysadmins hesitant to implement investigations on production systems due to potential performance impact. Improving performance would make admins more likely to use the tools to diagnose the problems. 

How does dropwatch work

Normally, monitoring for dropped packets requires the creation of a script that periodically polls all the aformentioned interfaces, checking for a change in various counter values. Dropwatch instead listens on a netlink socket for the kernel to inform userspace (apps like dropwatch and any others), that a packet has been dropped. This of course implies that the kernel has some sort of functionality to this end. That functionality (called the netlink Drop Monitor protocol), is currently being reviewed upstream. For those who would like to experiment with dropwatch now, you can either retrieve the appropriate kernel patches from the netdev mailing list, or download them ​here
How do I get dropwatch?

dropwatch is built and available with kernel support in Fedora. Currently if you want the code, you can browse it ​here. The git repository address is: ​git://git.fedorahosted.org/dropwatch.git if you want to clone the tree and tinker Once the kernel bits are in place, I'll package an official release ​here After that, I'll be packaging dropwatch as an rpm for fedora so you can just get it through yum. Other distros are welcome & encouraged to package for themselves as well. 


## 扩展阅读

* [dropwatch 网络协议栈丢包检查利器](http://blog.yufeng.info/archives/2497#more-2497)

