---
layout: post
title: 安装 Ruby 多版本管理工具 rvm
date: 2015-10-04
categories:
  - 技术
tags:
  - rvm
---
## rvm

[rvm](https://rvm.io/) 是一个 Ruby 多版本环境的管理和切换工具（类似的还有 [rbenv](https://github.com/sstephenson/rbenv) 等）。可以通过以下的方式进行安装：

__这里所有的命令都是再用户权限下操作的，任何命令最好都不要用 sudo__.


## rvm 安装

```bash

$ curl -L get.rvm.io | bash -s stable
$ source ~/.bashrc
$ source ~/.bash_profile
```

## 修改 RVM 的 Ruby 安装源到淘宝镜像服务器

```bash

# Mac
$ sed -i .bak 's!cache.ruby-lang.org/pub/ruby!ruby.taobao.org/mirrors/ruby!' $rvm_path/config/db

# LINUX
$ sed -i 's!cache.ruby-lang.org/pub/ruby!ruby.taobao.org/mirrors/ruby!' $rvm_path/config/db

# 或者
$ sed -i -e 's/ftp\.ruby-lang\.org\/pub\/ruby/ruby\.taobao\.org\/mirrors\/ruby/g' ~/.rvm/config/db
```


## 列出已知的 Ruby 版本

```bash

$ rvm list known
```

## 安装一个 Ruby 版本

```bash

$ rvm install 2.2.3
```

__这里安装了最新的 2.2.3, 是从 rvm list known 列表里面的都可以拿来安装_。


## 切换 Ruby 版本

```bash

$ rvm use 2.2.3
```

## 设置为默认版本

```bash

$ rvm use 2.2.3 --default 
```

## 查询已经安装的ruby

```bash

$ rvm list
```

## 卸载一个已安装版本

```bash

$ rvm remove 1.9.0
```


## 如果使用 Gemfile 和 Bundle

你可以用bundle的gem源代码镜像命令：

```bash

$ bundle config mirror.https://rubygems.org https://ruby.taobao.org
```

这样你不用改 Gemfile 的 source：

```bash

source 'https://rubygems.org/'
gem 'rails', '4.1.0'
...
```


## 扩展阅读

* [rvm 指南](https://www.ruby-china.org/wiki/rvm-guide)
* [RubyGems 淘宝镜像](https://ruby.taobao.org/)


## 祝大家玩的开心

## 编程之道，就在[编程之美]

![编程之美](/img/weixin_qr.jpg)

