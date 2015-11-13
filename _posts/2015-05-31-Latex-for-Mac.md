---
layout: post
title: Mac LaTex 实战
date: 2015-05-31
categories:
  - 技术
tags:
  - Latex
---

## 缘由

大部分时间都在用 [Markdown](https://guides.github.com/features/mastering-markdown/) 编写各类文档（包括，你在阅读的这些文章） ，但有时还是只有 LaTex 才能把事情搞得完美，比如写书这事。最近想写本书玩玩，所以在 Mac 上折腾 LaTex 了，分享日志出来，希望对大家有所帮助。具体，如下：（下图是 `ShareLaTex`） 

![](/img/article/05/2015-05-31-sharelatex.png)


## 软件安装

* Tex环境：[MacTex](http://www.tug.org/mactex/)
* 编辑环境：[TeXstudio](http://texstudio.sourceforge.net/)
* 安装字体：[Fonts](http://www.tug.org/mactex/fonts/index.html)


## 学习资料

* [Making your first PDF with LaTeX and Sublime Text 2 for Mac](http://economistry.com/2013/01/installing-and-using-latex-for-mac/)
* [LaTex 入门](http://www.jianshu.com/p/e59aaac15088)
* [部署MAC上的Sublime Text+LaTex中文环境](http://www.readern.com/sublime-text-latex-chinese-under-mac.html)
* 刘海洋的[《LaTeX入门》](http://book.douban.com/subject/24703731/)


## 在线 LaTex 协同编辑

如果你希望在线协同 LaTex 编辑，可以使用 [ShareLaTex - The easy to use, online, collaborative LaTeX editor](https://www.sharelatex.com/) 软件。如果你有机器，还可以自己搭建服务器，因为这个[系统已经开源](https://github.com/sharelatex/sharelatex)了。


## 图书编辑

如果你希望通过 LaTex 编辑出版自己的技术书，那么建议参考 陈硕 同学的 [typeset](https://github.com/chenshuo/typeset) 项目。他的[《Linux 多线程服务端编程：使用 muduo C++ 网络库》](http://book.douban.com/subject/20471211/)一书就是使用这个模板的，效果非常好。


### 配置 TeXstudio

* 启动 Texstudio，选择 TeXstudio-->Preferences-->"Configure Texstudio"-->Commands，XeLaTex 设置为 `/usr/local/texlive/2014/bin/x86_64-darwin/xelatex -synctex=1 -interaction=nonstopmode %.tex`；（xelatex 的路径因安装方式的不同，可能会有差异）

* 选择 Preferences-->"Configure Texstudio"-->Build， 然后：

（1）、设置 Build & View 为 `Compile & View`；

（2）、Default Compiler 由默认的 PdfLaTex 修改为 `XeLaTex`；

（3）、PDF Viewer 改为 `Internal PDF Viewer(windowed)`，预览时会弹出一个独立的窗口，方便查阅。

（4）、如果编译时，出现一些命令行工具找不到，那么可能你的 MacTex 安装路径和 TeXstudio 中的默认配置不一致。选择 TeXstudio-->Preferences-->"Configure Texstudio"-->Build，勾选 "Show Advanced options (左下角)", 然后在 "Additional Search Paths" 下面的 "Commands ($PATH)" 中填上你的 texlive 安装主目录（比如我的是`/usr/local/texlive/2014/bin/x86_64-darwin`

* 用 git 克隆 `typeset` 库；

* 用 TeXstudio 打开 `typeset.tex`，点击界面上的绿色箭头就可以开始编译了。


## 扩展阅读

* [使用 TeXstudio 编写 Latex (miktex)](http://blog.sina.com.cn/s/blog_4a238ec20101sl5n.html)
* [XeTeX：解決 LaTeX 惱人的中文字型問題](http://www.hitripod.com/blog/2011/04/xetex-chinese-font-cjk-latex/)
* [为 MacTeX 配置中文支持( TeXShop )](http://liam0205.me/2014/11/02/latex-mactex-chinese-support/)
* [LaTeX 常用功能](http://blog.csdn.net/solstice/article/details/638)
* 知乎，[有哪些好的 LaTeX 编辑器？](http://www.zhihu.com/question/19954023/answer/71112878?utm_campaign=webshare&utm_source=weibo&utm_medium=zhihu)
* [Making your first PDF with LaTeX and Sublime Text 2 for Mac](http://economistry.com/2013/01/installing-and-using-latex-for-mac/)
* [部署MAC上的Sublime Text+LaTex中文环境](http://www.readern.com/sublime-text-latex-chinese-under-mac.html)
* [sharelatex for chinese](https://www.sharelatex.com/learn/Chinese)
* [https://github.com/kemayo/sublime-text-git](https://github.com/kemayo/sublime-text-git)
* [一本法文LaTeX入门书籍LaTeX源码](http://www.latexstudio.net/archives/4705)


## 祝大家玩的开心

## 编程之道，就在[编程之美]

![编程之美](/img/weixin_qr.jpg)

