---
layout: post
title: Mac OSX "brew update"失败问题解决
date: 2015-05-18
categories:
  - 技术
tags:
  - Mac
---
## 预料之外

使用 Macbook Pro 也有小半年了，一切都 Ok，建议大家都去用 Mac （当然，也别被 池老师 忽悠了。什么《人生元编程》，其实都是扯淡）。 我使用 Mac 的标配 brew 进行软件包管理。发现 `brew update` 失败了，具体错误，如下：

``` shell

$ sudo brew update
^COne sec, just cleaning up
HEAD is now at dc84848 Always create real directories under lib/ruby
Error: Failure while executing: git pull -q origin refs/heads/master:refs/remotes/origin/master

```

于是研究了一番。发现 brew 还是奇葩的，原来 brew 在 `/usr/local` （可以通过执行 `brew --prefix` 命令查看具体路径）下维护了一个 `git` 代码库。用了 5+ 年 Fedora 系统，用的是 `yum` 没有这样的。不过，这样也好。 update 就成了，更新代码库了。在此目录执行 `git remote -v` 就可以查看代码库的上游地址了[https://github.com/Homebrew/homebrew.git](https://github.com/Homebrew/homebrew.git)。具体，如下：

``` shell

$ git remote -v
origin	https://github.com/Homebrew/homebrew.git (fetch)
origin	https://github.com/Homebrew/homebrew.git (pus
```

## 解决方案

了解了真相之后，问题就好解决了。只需要执行这个代码库的 git 管理操作命令就好了。具体操作，如下。

``` shell

$ sudo git fetch origin
$ sudo git reset --hard origin/master
$ sudo brew update
Updated Homebrew from 81a06515 to 81a06515.
$ sudo brew update
Password:
Already up-to-date.
```

## 事情还没完

虽然 brew update 是 Ok 了。但是，发现 `Vim` 挂了。具体错误，如下：

``` shell

$ vim
dyld: Library not loaded: /usr/local/lib/libruby.2.1.0.dylib
  Referenced from: /usr/local/bin/vim
  Reason: image not found
Trace/BPT trap: 5

```

如果足够细心的话，你会发现 brew 的代码库，其实是一个 ruby 代码库，应该是更新过程中将 `libruby` 搞没了。这下好了，只有重装 vim 了。具体操作，如下：

``` shell

$ sudo brew reinstall vim
$ sudo brew reinstall ruby
```

可以执行 `locate libruby.2.1.0.dylib` 看哪里有对应的库，然后 ln 一下。否则，需要重新安装`ruby 2.1.0`了。


热爱生活，热爱彩蛋，随时准备解决各种坑。



## 扩展阅读


## 祝大家玩的开心

## 编程之道，就在[编程之美]

![编程之美](/img/weixin_qr.jpg)

