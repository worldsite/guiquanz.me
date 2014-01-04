---
layout: post
title: Fedora 17安装开发、办公软件
date: 2012-07-30
categories:
    - 技术
tags:
    - fedora
    - git
    - vim
    - R
    - flex
    - lemon
    - bison
    - ruby
    - rails
    - cmake
    - scons
---
## 缘由

最近从`ubuntu 11.10`迁移到了`Fedora`平台。安装的是最新的Fedora 17版本（`x86_64`），用户体验还不错。以下是开发、办公等相关软件，命令行安装汇总，希望对初级用户有帮助。具体，如下：

备注: `本文仅适用于Fedora环境`。

## 软件安装

1.安装编辑器：`vim`
<pre class="prettyprint linenums">
sudo yum install vim
</pre>

2.安装`c/c++`等编译工具：`gcc/g++/erl/R`
<pre class="prettyprint linenums">
sudo yum install gcc gcc-c++ erl R 
</pre>

3.安装`c/c++帮助手册`
<pre class="prettyprint linenums">
sudo yum install man-pages libstdc++-docs
</pre>

4.安装软件编译工具：`scons/cmake`
<pre class="prettyprint linenums">
sodu yum install scons cmake
</pre>

5.安装词法/语法解析工具：`flex/lemon/bison`
<pre class="prettyprint linenums">
sudo yum install flex bison lemon
</pre>

6.安装数据库及开发包： `mysql`
<pre class="prettyprint linenums">
sudo yum install mysql mysql-server mysql-devel 
</pre>

7.安装版本控制工具：`git/svn`
<pre class="prettyprint linenums">
sudo yum install git-core svn
</pre>

8.安装Rails开发环境：`ruby/rails`
<pre class="prettyprint linenums">
sudo yum install ruby ruby-devel
sudo gem sources -a http://gems.github.com
  # 否则，会报错：
  # ERROR:  While executing gem ... (Zlib::GzipFile::Error)
  #  not in gzip format 
sudo gem update
sodu gem install rails
</pre>

9.安装输入法：`sunpinyin`
<pre class="prettyprint linenums">
sudo yum install ibus ibus-sunpinyin
</pre>

10.安装特殊压缩工具：`7zip/Rar`
<pre class="prettyprint linenums">
sudo yum install p7zip p7zip-plugins unrar
</pre>

11.安装音/视频播放器及解码包
<pre class="prettyprint linenums">
sudo rpm -ivh http://download1.rpmfusion.org/free/fedora/rpmfusion-free-release-stable.noarch.rpm
sudo yum makecache
sudo yum install ffmpeg ffmpeg-libs gstreamer-ffmpeg xvidcore libdvdread libdvdnav 
sudo yum install lsdvd gstreamer-plugins-good gstreamer-plugins-bad gstreamer-plugins-ugly
sudo yum install audacious-plugins-freeworld-mp3
</pre>

12.安装办公软件：`OpenOffice`
<pre class="prettyprint linenums">
sudo yum groupinstall office/productivity
sudo yum install openoffic.org-langpack-zh_CN #如果安装时，桌面已是中文了，那不用执行此操作
</pre>

13.安装浏览器：`Chrome`

（1）到官网下载Linux平台安装包

（2）安装chrome
<pre class="prettyprint linenums">
sodu yum install pax* redhat-lsb*
  # 否则，会报错：
  # warning: google-chrome-stable_current_x86_64.rpm: Header V4 DSA/SHA1 Signature, key ID 7fac5991: 
  # NOKEY error: Failed dependencies:
  # lsb >= 4.0 is needed by google-chrome-stable-20.0.1132.57-145807.x86_64
sodu yum sudo rpm -ivh google-chrome-stable_current_x86_64.rpm
</pre>

