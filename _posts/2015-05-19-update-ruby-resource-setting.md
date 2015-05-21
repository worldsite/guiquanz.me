---
layout: post
title: 解决“gem update”被墙问题
date: 2015-05-19
categories:
  - 技术
tags:
  - gem
---
## 在大炮高墙下茁壮成长

在天朝搞点东西真不容易，使用 gem 都经常被墙，怎么破呢？如果购置了非常稳定的VPN，那应该没事。否则，需要修改配置了。因为 rubygems.org 使用了亚马逊的存储，所以被墙了，需要修改源链接。

### 查看数据源

``` shell

$ sudo gem update --system
$ gem sources -l
```

### 修改数据源

在国内可以使用淘宝的数据源，不过 https 也被墙了，只能用 http 链接。同时，这个 http://rubygems.org 非安全链接也能使用。

``` shell

$sudo gem sources --remove https://rubygems.org/
$sudo gem sources -a http://ruby.taobao.org/
$gem sources -l
```

## 扩展阅读

* [Setup Ruby On Rails on Mac OS X 10.10 Yosemite](https://gorails.com/setup/osx/10.10-yosemite)


## 祝大家玩的开心

## 编程之道，就在[编程之美]

![编程之美](/img/weixin_qr.jpg)

