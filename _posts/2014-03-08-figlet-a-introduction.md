---
layout: post
title: 巧用figlet生成ASCII艺术字
date: 2014-03-08
categories:
  - 技术
tags:
  - figlet
  - tools
---
## figlet

`figlet`[http://www.figlet.org](http://www.figlet.org)是，一块不错的开源小工具，专门用于生成ASCII`文本化`的`艺术字`。可以满足小众对文本个性化的需求，如艺术化的名字，抑或为自己的开源项目弄一个个性化的名字符号等等（我就有这个强烈的需求）。好软件，推荐大家使用。

## 安装figlet

<pre class="prettyprint">
mkdir ~/figlet && cd ~/figlet
 #我们下载的是figlet-2.2.5版本
wget ftp://ftp.figlet.org/pub/figlet/program/unix/figlet-2.2.5.tar.gz
tar xfvz figlet-2.2.5.tar.gz
cd figlet-2.2.5
make 
sudo make install
</pre>

## 牛刀小试

命令很简单，就是`figlet <需要特殊处理的字符串>`。当然，我们可以通过`-c`参数指定居中显示，`-f`指定字体类型（通过`figlist`查看目前支持的自体。也可以从官网[下载自体](http://www.figlet.org/examples.html)），`-w`指定字符宽度。具体操作，如下：

[![figlet](/img/article/2014-03/08-01_figlet.png)](http://www.figlet.org)


## 扩展阅读


## 祝大家玩的开心

## 编程之道，就在[编程之美]

![编程之美](/img/weixin_qr.jpg)

