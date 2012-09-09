--- 
layout: post
title: 搭建Rails开发环境
date: 2012-07-24
categories:
    - 技术
tags:
    - rails
---
## 缘起

好久没动Rails了，打算重新搭建开发环境。安装[ruby](http://www.ruby-lang.org/en)1.9.3和[Rails](http://rubyonrails.org)3.2.6。平台环境为: Ubuntu 11.10 x86_64。

备注: `本文仅适用于Ubuntu环境`。

0、创建工作目录
<pre class="prettyprint linenums">
mkdir -p ~/ruby && cd ~/ruby
</pre>

1、安装yaml库
<pre class="prettyprint linenums">
wget http://pyyaml.org/download/libyaml/yaml-0.1.4.tar.gz
tar xzvf yaml-0.1.4.tar.gz
cd yaml-0.1.4
./configure --prefix=/usr/local
make
sudo make install
</pre>

采用apt-get工具安装libyaml
<pre class="prettyprint linenums">
sudo apt-get install build-essential bison
sudo apt-get install libyaml-dev
</pre>

2、安装ruby-1.9.3
<pre class="prettyprint linenums">
wget http://ftp.ruby-lang.org/pub/ruby/1.9/ruby-1.9.3-p194.tar.gz
tar xzvf ruby-1.9.3-p194.tar.gz
cd ruby-1.9.3-p194
./configure --prefix=/opt/ruby-1.9.3-p194 --enable-shared --disable-install-doc --with-opt-dir=/usr/local/lib
make
sudo make install
</pre>

3、更新gem 
<pre class="prettyprint linenums">
sudo gem update --system
sudo gem update
</pre>

4、安装rails
<pre class="prettyprint linenums">
sudo gem install rails
</pre>

5、安装MySQL
<pre class="prettyprint linenums">
sudo apt-get install mysql mysql-server 
</pre>

6、创建一个rails项目，使用mysql数据库
<pre class="prettyprint linenums">
rails new hentaier --database=mysql
</pre>

7、启动web服务
<pre class="prettyprint linenums">
cd hentaier && rails server
</pre>

8、察看结果
 浏览 http://localhost:3000 ，正常显示如下结果：
<section id="lightboxthumbs">
<a href="/images/article/rails-hw.png" rel="lightbox" title="Rails"><img src="/images/article/rails-hw.png" alt="运行结果" class="frameit" width="400px" height="240px"></a>
</section>

