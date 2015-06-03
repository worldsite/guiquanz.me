---
layout: post
title: 用Kindle DX阅读代码
date: 2014-11-27
categories:
  - 技术
tags:
  - 阅读
---
## 一屏幽梦

一直想弄个舒服代码阅读平台，最早尝试过`汉王的电纸书`（跟客服说需要彩色的，不信吧，现在都死了），后来又试了`ipad min`等都觉得不太理想。最后，只有拿`Kindle DX`问路了。Kindle DX的整体效果不错，当然也有自己的固有缺点：相对比较重、无背光、没有灯、存储小、响应慢、功能单一、屏比较脆弱……。已经停产了，如想买，请从速，淘宝[故人归](http://item.taobao.com/item.htm?spm=a230r.1.14.1.44oOrh&id=21100379483&ns=1&abbucket=10#detail)，正品没得说。少说废话了，目前基本满足我的需求。可是，问题又来了？怎么搞定各种项目的代码电子书呢？(感慨一下：其实，[Linux Cross Reference](http://lxr.free-electrons.com/source/crypto/)的存在价值还是非常大的)

我的方案是，采用[agentzh](https://github.com/agentzh)大神的[src2kindle](https://github.com/agentzh/src2kindle)工具的优化版（整理好之后，会开源），配合[calibre](http://www.calibre-ebook.com/)工具，生成mobi文件，然后放到Kind DX上阅读。电子书制作具体流程，大致如下（以为`libngx`项目为例）：

![](/img/article/11/2014-11-28-03.png)


## 软件安装流程

```bash

# 安装 calibre

$ sudo -v && wget -nv -O- https://raw.githubusercontent.com/kovidgoyal/calibre/master/setup/linux-installer.py | sudo python -c "import sys; main=lambda:sys.stderr.write('Download failed\n'); exec(sys.stdin.read()); main()"

$ mkdir ~/mobi
$ mkdir ~/src2kindle && cd ~/src2kindle
$ git clone https://github.com/agentzh/src2kindle

$ vim ~/.bash_profile
# 追加以下内容，然后保存、退出
export PATH=$PATH:~/src2kindle

$ . ~/.bash_profile

# 测试
$ cd ..
$ git clone https://github.com/guiquanz/libngx
$ src2kindle libngx

```

BTW，准备对一些有趣的开源项目生成mobi电子书文件，在[YewSauce](https://github.com/YewSauce)上贡献给需要的人（也便于不爱折腾，不喜欢perl的小伙伴们），hacking……，根本停不下来啊。__记住：纸和笔，才是最好的工具。kindle只是一旁的屏，加油吧。__


另外，如果想要使用`Calibre`的 `ebook-convert`工具，通过编写`recipes`下载网页并生成mobi图书，可以参考[抓取网页内容生成Kindle电子书](http://blog.codinglabs.org/articles/convert-html-to-kindle-book.html)一文。

Amazon也推出[Kindle Textbook Creator](https://kdp.amazon.com/how-to-publish-educational-content?ref_=GS)了，官方的工具，大家可以关注一下。


## 扩展阅读

* [抓取网页内容生成Kindle电子书](http://blog.codinglabs.org/articles/convert-html-to-kindle-book.html)
* [Amazon 推出个人图书制作工具 Kindle Textbook Creator，轻轻松松将所有的 PDF 变成 Mobi](http://www.36kr.com/p/219029.html)
* [Kindle Textbook Creator](https://kdp.amazon.com/how-to-publish-educational-content?ref_=GS)

## 祝大家玩的开心

## 编程之道，就在[编程之美]

![编程之美](/img/weixin_qr.jpg)

