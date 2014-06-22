---
layout: post
title: 我的代码管理之道
date: 2014-06-01
categories:
  - 技术
tags:
  - git
---
## 痛点

每一位优秀的开发者，都有很多自己的code base（俗称代码基库）以及私人项目（公司的项目，不在我的讨论范畴）。该如何管理，才能保证代码的安全以及高可用呢？今天，就来聊聊我的代码管理之道。

## 我的代码管理之道

首先，需要一个VCS工具来管理代码的版本。我自己是使用[git](http://www.git-scm.com),进行代码版本管理的，这也是下文的前提：

1、本地代码： 采用git系统管理，适当进行备份；如果自己有机器，推荐部署[gogs](https://github.com/gogits/gogs)或[gitlab](https://www.gitlab.com/)进行管理（会产生不少管理、维护成本）。

2、开源项目： 使用github平台进行管理；

3、私有项目： 如果你特别注重安全，那还是本地管理。否则，可以选择[https://bitbucket.org/](https://bitbucket.org/)进行免费管理。当然，如果愿意为其付费，那可以使用[https://github.com/](https://github.com/)。云平台可以为你提供非常高的可用性，和代码备份，但请谨记代码的安全性是无法保证。云从来都不是为了安全而存在的。

以上就是我的代码管理之道，对于创业公司，也很适用。

![code reps](/img/article/06/2014-06-01_01-code-reps.png)

BTW，这个图是通过[kityminder](https://github.com/fex-team/kityminder)，[在线脑图编辑器](http://naotu.baidu.com/)绘制的。

## 扩展阅读


## 祝大家玩的开心

## 编程之道，就在[编程之美]

![编程之美](/img/weixin_qr.jpg)

