---
layout: post
title: 巧用diff和patch维护代码补丁
date: 2014-04-07
categories:
  - 技术
tags:
  - diff
  - patch
---

`diff`是*nix系统上，文本文件差异比较的常用工具。类似的工具在CVC（版本控制系统）中，如git，svn等工具，有广泛的应用。而`patch`是diff文件应用工具，即将差异应用到指定的文件（简单文件或目录文件）中，也就是所谓的“补丁应用”。`diff`和`patch`是一组非常实用的工具，对于代码管理，更是如此。

我们，可能经常要修改一些开源软件，然后以patch的方式提交代码变更，而不是提交整个代码（当然，有了`github`这样的开放平台，我们可以通过pullrequest提交），也便于别人更容易去升级软件。当你一个人维护着很多代码变更时，`diff`和`patch`就非常重要了，[agentzh](https://github.com/agentzh)大神的[ngx_openresty](https://github.com/agentzh/ngx_openresty/tree/master/patches)，就是一个很好的案例。他用patch维护着ngx_openresty用到的很多开源软件补丁。怎么便捷的制作和应用补丁呢？

## 一个简单示例

``` bash

    diff A B > C  #生成A和B的diff文件C
    patch A C     #给A打上diff文件得到B
    patch -R B C  #B还原为A
```


## ngnix补丁

生成补丁文件：

``` bash

diff -urN nginx-1.5.10 nginx-1.5.11 > diff.patch
```

升级nginx：

``` bash

cp diff.patch nginx-1.5.10

cd nginx-1.5.10

patch -p1 < diff.patch
```

## 扩展阅读


## 祝大家玩的开心

## 编程之道，就在[编程之美]

![编程之美](/img/weixin_qr.jpg)

