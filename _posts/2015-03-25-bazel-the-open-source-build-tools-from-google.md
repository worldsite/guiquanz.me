---
layout: post
title: bazel - Google御用大规模软件构建工具终于开源了 
date: 2015-03-25
categories:
  - 技术
tags:
  - 软件构建工具
---
## bazel 的开源

最近程序猿们的福利不断，真是喜大普奔了。首先，微软继 `.NET 开源`之后，又开源了 Visual Studio 构建工具 [MSBuild](https://github.com/Microsoft/msbuild ) （微软还承诺会将其迁移至 Linux 和 Mac 平台上）。然后，Facebook近日开源了增强网络流量控制工具 [ATC](https://github.com/facebook/augmented-traffic-control)(ATC能够模拟2G、2.5G（Edge）、3G和LTE4G网络环境，测试工程师可以快速在各种不同模拟网络环境中切换。)。然后，github公司开源了其御用的CSS工具[Primer](http://primercss.io/)。然后，google终于也不甘落后，开源了自家面向云的大规模软件构建工具[bazel](http://bazel.io/)系统(还记得`腾讯`的系统构建工具[typhoon-blade](
https://github.com/chen3feng/typhoon-blade)吗？那就是 @吴军 博士去腾讯之后领了一邦让人仿照`bazel`开发的。另外，Google的其他离职员工开发的`buck`和`Pants`也是基于`bazel`模型的）。至此，各大知名的构建工具都开源了。


## bazel 的主要特点

bazel 是一块非常快速和可靠的构建代码的工具，由Java等语言编写的而成。用于构建Google绝大多数的软件，是为了解决Google开发环境中出现的各种问题而设计开发的（好赞啊）。bazel 的主要特点，如下：

* 一个大规模的，共享代码库，在这里软件都是从源码构建出来的。Bazel 通过缓存和并行机制，达到快速编译的目标。Google大规模软件开发实践的中坚力量之一。

* 自动化测试和发布的增强。保证在持续构建或发布流水线上的机器上生成的输出和开发人员机器生成的二进制一致（bitwise-identical）。

* 支持多种语言和平台。目前支持Java，C/C++等语言代码的构建。


## 尝鲜

Google 还在官网上，针对 `Make`、`Ninja`、`Ant`、`Maven`、`Gradle`、`Buck` 和 `Pants` 进行了横向的比较，业界的良心啊。喜欢尝新的同学，可以去玩一下了。我目前没有时间，暂时不试了。其实，我对其规则系统还是特别感兴趣的。


## 美中不足

从[bazel](https://github.com/google/bazel/)开源项目说明来看，目前开源的是一个本地（local）编译的版本，也就是说没有集群脚本，不能用于大规模云编译。希望这一块，后续还会开源，否则竞争力就大打折扣了。


## 扩展阅读

* `bazel` 源码 [https://github.com/google/bazel](https://github.com/google/bazel/)


## 祝大家玩的开心

## 编程之道，就在[编程之美]

![编程之美](/img/weixin_qr.jpg)

