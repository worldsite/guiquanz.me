--- 
layout: post
title: Rebol终于开源了
date: 2012-12-21
categories:
  - 技术
tags:
  - Rebol
---

## R3的开源

![](/img/article/rebol.gif)

R3，就是Rebol语言第三个大版本。这个版本，终于在2012年12月12日，这个特别的日子开源了。以下是[Carl Sassenrath](http://en.wikipedia.org/wiki/Carl_Sassenrath)的Notes：(**我是在2012年12月21日，这个更特殊的日子在家为R3做这个标记。你若在上班，那就Te悲催了。哈哈！**）

    R3 Source Code Released!

    Carl Sassenrath, CTO 
    REBOL Technologies 
    12-Dec-2012 10:33 GMT 
     
    You probably thought the source release would never happen? Am I right?

    Well, it's there now in github. This is preliminary. Once I know some of you have built it successfully, I'll make a more public announcement and add a tarball for download from rebol.com.

    I'll check in here for a several minutes every night to respond to any questions.

    Let's see what happens... Who will be the first to port it to Android?

    -Carl

    Soon: A roadmap of where I'd like to see R3 go...

为了响应大家的强烈呼声，Carl Sassenrath最终选择了GitHub为R3的代码托管平台。现在可以通过[https://github.com/rebol/r3](https://github.com/rebol/r3)访问R3的代码了，并贡献你的聪明才智。


## 源码安装R3

**仅针对 Fedora 17 x86-64平台环境**

释放出来的R3代码支持很多平台，但针对Linux平台仅支持32位的编译模式。以Fedora 17 x86-64平台为例，编译过程如下：

<pre class="prettyprint linenums">

# 安装32位的glibc头文件
sudo yum install glibc-devel.i686

# 克隆代码库
mkdir ~/rebol && cd ~/rebol
git clone git://github.com/rebol/rebol.git r3

# 下载预编译的r3,并改为r3-make
cd r3/make
wget http://www.rebol.com/r3/downloads/r3-a111-4-3.tar.gz
tar zxfv r3-a111-4-3.tar.gz
mv r3 r3-make

# 编译r3
make prep
make
</pre>


## 小试牛刀

*1. 命令行交互式操作:*
<pre class="prettyprint linenums">
~/rebol/r3/make
./r3

>> print "Hello, World!"
Hello, World!
>> 
>> quit
</pre>

*2. 执行R3文件：*
<pre class="prettyprint linenums">
vim t1.r
#!./r3
REBOL [Title: "Example script"]
print "Helloe, Wolrd!";

# 退出编辑

chmod 755 ./t1.r
./t1.r  #执行文件： 也可以执行 ./r3 ./t1.r
</pre>

接下来要抽空好好研究一下R3，这可是非常值得的一件事情。


## 扩展阅读

1. [Rebol官网](http://rebol.com)；

2. [Rebol文档](http://www.rebol.com/docs.html);

3. [更多Rebol编程示例](http://www.rebol.net/cookbook)。


