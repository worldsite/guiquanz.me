---
layout: post
title: chromium系统构建工具一览
date: 2014-07-26
categories:
  - 技术
tags:
  - 系统构建工具
---
C/C++依然是最核心的系统开发语言。针对不同平台，不同规模的项目，都有不同的构建工具。如GNU auto*,以及古老的make以及最近进兴起的cmake等等（不想深入描述）。今天在看[chromium](http://code.google.com/p/chromium)源码时，发现其构建使用的是特有的工具，而且已经迭代了好几个。是的，裸体的__chromium__都已经`769.7M`了，虽然其中还有很多`python`等非C/C++代码，但是再用make直接编译显然是不行的（会特别慢，而且不太好组织源码……)。

`chromium`的构建工具，已经从[gyp](https://code.google.com/p/gyp)，到[ninja](http://martine.github.io/ninja/)，向[gn](http://code.google.com/p/chromium/wiki/gn)演变。这无疑是一个缓慢而痛苦的过程，也是一个必然的选择。随着代码的膨胀，需要越來越高效而便捷的工具来管理系统的构建，把人力从复杂而重复的流程中释放出来。

从目前的代码来看chromium同时支持`gyp`,和`ninja`。网上有传言`gyp`太复杂了，所以有了`ninja`，……虽然ninja非常高效，但是当工程目录结构复杂，源码文件量非常大的时候，手工编写和维护`build.ninja`（build.ninja是ninja默认的编译规则文件。写.ninja也像Makefile一样，需要顾虑每一个小细节。总体的语法抽象程度还是不够，用户友好性一般）这又成了非常浩大而繁琐的工程。针对chromium这样的项目，这个问题就非常严重了。为了解决`.ninja`文件编辑和维护存在的问题，`gn`就应运而生了。gn采用更高的抽象，用户友好的语法，来编写构建文件.gn。gn本身只是一个翻译工具，是将`.gn`文件翻译为`.ninja`（当然也可以翻译为.gyp文件。这应该是过渡期间的一个策略）。最后的编译工作是由ninja根据gn生成的.ninja来编译的。这就是chromium未来的系统构建方式。

`gyp`，`ninja`和`gn`，本身是一些复杂的工具，一般不适合也不建议简单的小项目使用（需要考虑工具使用的成本（学习成本等）、工具依赖的特殊环境等等。针对此类工程建议使用作者在[sundial](https://github.com/guiquanz/sundial)开源项目中附带的构建系统工具）。但，对于有能力的开发者，还是建议学习了解一下。代码之下无秘密，可以学习工具的使用（至少可以看懂现有项目的配置等），学习别人解决问题的思路……总会有很多意外的收获。没准哪天你都是`chromium`等大型开源项目的的开发者。


## 扩展阅读

* [chromium GN build quick-start](http://code.google.com/p/chromium/wiki/GNQuickStart)
* [chromium ipc BUILD.gn](http://src.chromium.org/viewvc/chrome/trunk/src/ipc/BUILD.gn)
* [ninja: a small build system with a focus on speed](http://martine.github.com/ninja)
* [Ninja manual](http://martine.github.io/ninja/manual.html)
* [Ninja demo](http://martine.github.io/ninja/build.ninja.html)
* [Ninja doxygen documentation](http://martine.github.io/ninja/doxygen/index.html)
* [google gyp](https://code.google.com/p/gyp)
* [How to hack on GYP](https://code.google.com/p/gyp/wiki/GypHacking)
* [gyp on github](https://github.com/martine/gyp)


## 祝大家玩的开心

## 编程之道，就在[编程之美]

![编程之美](/img/weixin_qr.jpg)

