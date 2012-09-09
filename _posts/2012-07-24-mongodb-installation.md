---
layout: post
title: 从源码安装mongodb
date: 2012-07-24
categories:
    - 技术
tags:
    - mongodb
---

## 缘起

`mongodb`是一个主流的面向文档的非关系数据库（NOSQL）产品，国内主要有"视觉中国"、"盛大云"等系统/平台在使用。之前安装mongodb用的是 [官网](http://www.mongodb.org/downloads)Linux 64-bit的二进制包。现在想从源代码进行编译安装，然后进一步分析其实现。由于mongodb依赖于[Boost库](http://www.boost.org),所以需要先安装Boost库。具体安装流程，如下：

提示：`编译过程很慢，并且需要准备 > 4.2G的磁盘空间，Boost占1G左右，mongodb占3.2G左右。本文仅适用于Ubuntu环境`。

0、创建工作目录
<pre class="prettyprint linenums">
mkdir -p ~/mongodb && cd ~/mongodb 
</pre>
1、下载源代码包到当前目录

（1）、下载boost_1_49_0
<pre class="prettyprint linenums">
wget http://sourceforge.net/projects/boost/files/boost/1.49.0/boost_1_49_0.tar.gz
</pre>
如果下不了，直接HTTP方式下载，其URI如下：
http://sourceforge.net/projects/boost/files/boost/1.49.0/boost_1_49_0.tar.gz/download
当然，也可以安装mongodb自带的boost精简版本。

（2）、下载mongodb

下载2.2.0 RC0版本，对应mongodb-mongo-r2.2.0-rc0-0-g33dc844.tar.gz
访问 https://github.com/mongodb/mongo/tags页面，下载 mongodb-mongo-r2.2.0-rc0-0-g33dc844.tar.gz包到当前目录。

2、安装boost
<pre class="prettyprint linenums">
tar xzvf boost_1_49_0.tar.gz
cd boost_1_49_0
sudo ./bootstrap.sh
sudo ./b2
sudo ./bjam --toolset=gcc install
</pre>

3、安装mongodb

（1）、安装scons工具

由于mongodb采用scons管理软件的编译，所以需要先安装scons。具体操作，如下：
<pre class="prettyprint linenums">
sudo apt-get install scons # scons是一个基于python的工具软件构建工具
</pre>

（2）、安装mongodb
<pre class="prettyprint linenums">
tar xfvz mongodb-mongo-r2.2.0-rc0-0-g33dc844.tar.gz
cd mongodb-mongo-r2.2.0-rc0-0-g33dc844
scons .  #编译所有模块，如果只想编译数据库，则执行 scons mongodb 即可
sudo scons --prefix=/opt/mongo install #如果不想实际安装，不要执行此操作
</pre>

4、克隆mongdb库

如果要研究mongodb实现，就应该拥有其代码库，这个可以从[github](http://www.github.com/mongodb/mongo)上克隆。如果你还不是github的用户，赶紧去注册一个吧，在这里你会发现很多有趣的项目（当然，可以和别人分享自己的成果）。
<pre class="prettyprint linenums">
git clone git://github.com/mongodb/mongo.git
</pre>
如果没有git工具，采用以下方式安装：
<pre class="prettyprint linenums">
sudo apt-get install git-core
</pre>

5、可移植性

采用boost及scons在一定程度上会影响mongodb的可移植性。从官网看，目前主要支持以下平台：

二进制包：
OS X 32-bit
OS X 64-bit	
Linux 32-bit
Linux 64-bit	
Windows 32-bit
Windows 64-bit	
Solaris 64-bit

其他安装包：
For MacPorts, see the mongodb package.
For Homebrew, see the mongodb formula.
For FreeBSD, see the mongodb and mongodb-devel ports.
For ArchLinux, see the mongodb package in the AUR.
For Debian and Ubuntu, see Ubuntu and Debian packages.
For Fedora and CentOS, see CentOS and Fedora packages.
For Gentoo, MongoDB can be installed by running emerge mongodb. See Gentoo Packages.

拿到源代码包，然后自己折腾。


