--- 
layout: post
title: 利用XMind绘制思维导图
date: 2012-12-01
categories:
  - 技术
tags:
  - XMind
---

## 缘由

思维导图是一类专门用于高效捕捉和绘制，思维活动过程的软件。其中，以[MindManager](http://www.mindjet.com/)最知名，但这是一块商业软件，价格也很高。同类产品很多，其中开源的也不少，本文介绍的XMind就是一块不错的开源软件。


## XMind简介

[XMind](http://www.xmind.net/)是一款同样开源且跨平台的思维导图软件，在功能上不逊色于FreeMind，某些方面,甚至更加具有优势。而且，XMind 支持中文简、繁体。

XMind的特点是具有多种结构样式，不只Map一种，还包括Org、Tree、Logic Chart、Fishbone等。同时，除了可以灵活的定制节点外观、插入图标外，还有多种样式和主题可以选择。

在格式支持方面，可以：

导入： FreeMind、MindManager等软件的文件格式，当然也包括XMind自身的格式；

导出： 支持文本、图像、HTML 等。

`强调分享`是XMind的另一个特色。XMind.net网站本身便是一个分享思维导图的平台。在XMind中，你可以直接将想要分享的思维导图上传。

XMind包括独立版本和适用于Eclipse的插件版本，可以运行于Linux（包括 64 位）及Windows等平台。


## 安装XMind

**此操作，仅针对 Fedora 17平台**

1、下载安装包

<pre class="prettyprint linenums">
mkdir -p ~/xmind
cd ~/xmind
wget http://www.xmind.net/xmind/downloads/xmind-linux-3.3.0.201208102038_amd64.deb
</pre>


2、部署XMind

<pre class="prettyprint linenums">
cd ~/xmind
ar -x xmind-linux-3.3.0.201208102038_amd64.deb
tar xfv data.tar.gz
sudo cp -r usr/local/xmind /opt/
sudo cp -r usr/share /usr/
tar xfv control.tar.gz
sudo sh postinst
sudo sed -i 's/usr\/local/opt/g' /usr/share/applications/xmind.desktop
</pre>


3、启动XMind

<pre class="prettyprint linenums">
/opt/xmind/XMind
</pre>


## XMind小试

安装完XMind之后，我们试用她来绘制以上的部署流程，具体如下：
<a href="/img/article/XMind-Installation-for-Fedora-17.png" rel="lightbox"><img src="/img/article/XMind-Installation-for-Fedora-17.png" class="frameit" width="400px" height="240px"/></a>


## 扩展阅读

同类软件还有FreeMind、Freeplane、XYM、Inspiration等，可以通过[开源中国社区](http://www.oschina.net/p/xmind)了解更多的信息。

