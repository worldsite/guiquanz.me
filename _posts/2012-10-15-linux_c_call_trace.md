--- 
layout: post
title: 巧用Graphviz和pvtrace等工具可视化C函数调用
date: 2012-10-15
categories:
  - 技术
tags:
  - Graphviz
  - c
  - pvtrace
  - addr2line
  - dot

excerpt: <dl class="nr">
 <dt><img src="/img/article/pvtrace_test1.png"/> </dt>
 <dd>
 <p> 在分析复杂的C/C++软件时，如果有一个工具可以便捷的生成“函数调用关系图”，不是一件很好的事吗？如果你庆幸是一个Javaer或钟爱基于IDE（如Eclipse）的软件开发，应该会经常使用类似的工具。如果，你是*Nixer（*nix用户）呢？......</p>
 </dd> </dl>
---

## 引子

在分析复杂的C/C++软件时，如果有一个工具可以便捷的生成“函数调用关系图”，不是一件很好的事吗？如果你庆幸是一个Javaer或钟爱基于IDE（如Eclipse）的软件开发，应该会经常使用类似的工具。如果，你是\*Nixer（\*nix用户）呢？其实,我们一样有工具可用（地球村那么多hacker，你遇见的问题，多半是别人早就碰到了并给出了相应的解决方案）。

除了使用[CodeViz](http://www.csn.ul.ie/~mel/projects/codeviz/)、[egypt](http://www.gson.org/egypt/egypt.html)和[ncc](http://students.ceid.upatras.gr/~sxanth/ncc)，你可以尝试一下本文介绍的方案（核心的处理方式都差不多）。


## 实现原理

依赖于gcc的hook机制，在函数的入口及出口打上“标签”用于获取“调用者”函数符号地址信息（保存到文件中），然后通过addr2line（pvtrace内部实现依赖于此工具），根据给定的“地址”从可执行文件中查出对应的“函数名”。最后，生成满足graphviz组件dot语法的文件，用dot将其转为图形文件即可。

具体涉及的hook，如下：

1\. 函数入口及出口hook函数原型
<pre class="prettyprint linenums">
void __cyg_profile_func_enter( void *, void * ) 
	__attribute__ ((no_instrument_function));

void __cyg_profile_func_exit( void *, void * )
	__attribute__ ((no_instrument_function));
</pre>

  通过实现以上原型的实例函数，完成函数调用信息采集。

2\. 在调用main函数之前及其退出之后，设置特殊处理操作的hook函数原型
<pre class="prettyprint linenums">
void main_constructor( void )
	__attribute__ ((no_instrument_function, constructor));

void main_destructor( void )
	__attribute__ ((no_instrument_function, destructor));
</pre>

  通过实现以上原型的实例函数，生成及关闭用于保存函数调用关系信息的文件（trace.txt）。

具体的实现，可参考pvtrace源代码中的`instrument.c`文件。

更多细节，请查阅[用Graphviz可视化函数调用](http://www.ibm.com/developerworks/cn/linux/l-graphvis)一文。


## 安装pvtrace和Graphviz

1\. 安装pvtrace
<pre class="prettyprint linenums">
$ mkdir -p ~/project1 && cd ~/project1
$ wget http://www.mtjones.com/developerworks/pvtrace.zip
$ unzip pvtrace.zip -d pvtrace
$ cd pvtrace
$ make
$ sudo make install

# 查看pvtrace相关文件
$ ls -1 pvtrace
instrument.c
Makefile
stack.c
stack.h
symbols.c
symbols.h
trace.c
</pre>

2\. 安装graphviz
<pre class="prettyprint linenums">
$ sudo yum install graphviz
</pre>


## 测试

在完成软件安装之后，编写一个测试程序（test.c），并进行测试。具体流程，如下：

1\. 编辑测试文件test.c
<pre class="prettyprint linenums">
$ cd ~/project1
$ cat << EOF > test.c
#include <stdio.h>
#include <stdlib.h>

void test1()
{
    printf("in test1.\n");
}

void test2()
{
    test1();
    printf("in test2.\n");
}

void test3()
{
    test1();
    test2();

    printf("in test3.\n");
}

int main(int argc, char *argv[])
{
    printf("Hello wolrd.\n");

    test1();
    test2();
    test3();

    return 0;
}
EOF
</pre>

2\. 编译测试程序
<pre class="prettyprint linenums">
$ gcc -g -finstrument-functions test.c ./pvtrace/instrument.c -o test

注意： 必须有`-g -finstrument-functions`选项，否则后续就采集不到信息了。
</pre>

3\. 执行程序，生成信息文件trace.txt
<pre class="prettyprint linenums">
$ ./test
</pre>

4\. 通过pvtrace、可执行文件及trace.txt，生成信息文件graph.dot
<pre class="prettyprint linenums">
$ pvtrace test
</pre>

5\. 通过dot工具将graph.dot，转为图像文件graph.png
<pre class="prettyprint linenums">
$ dot -Tpng graph.dot -o graph.png
</pre>

6\. 浏览生成的图片

最终生成的图形效果，如下：

![](/img/article/pvtrace_test1.png)


## 为已有的项目生成函数调用图

可以通过以下步骤为已有的项目生成函数调用图：

1\. 将pvtrace源代码中的`instrument.c`文件拷贝到项目中；

2\. 增加对instrument.c文件的编译

3\. 修改`编译`选项：增加`-g -finstrument-functions`

4\. 修改`连接`选项：将`instrument.o`连接到可执行文件中

5\. 执行你的程序

6\. 用pvtrace及“你的可执行文件”处理trace.txt

7\. 用dot生成函数调用关系图

以下是对redis-2.4.17版本的处理，然后生成redis-cli启动及一个set操作对应的函数调用关系图：

<a href="/img/article/pvtrace_redis-cli.png" rel="lightbox"><img src="/img/article/pvtrace_redis-cli.png" class="frameit" width="400px" height="240px"/></a>


## 支持C++的扩展

目前pvtrace不支持C++代码，如果有人希望改进，一种可行的改进思路，如下：

1\. 修改instrument.c文件，支持C++环境的编译；

2\. 通过`c++filt`工具处理解析到的函数名标签，解析出实际的函数名： 为了支持继承、多态及函数重载等，C++编译时对函数名进行了特殊处理；

3\. 采用合理的编码方式，确保步骤2中生成的函数名满足dot的语法（C++是用整个函数原型等信息来生成的函数签名的，所以步骤2中用`c++filt`翻译出来的是函数原型（包括名字空间等信息））；

4\. 增加函数调用先后顺序的标识。


## 同类工具

1\. [CodeViz](http://www.csn.ul.ie/~mel/projects/codeviz/): A CallGraph Visualiser;

2\. [egypt](http://www.gson.org/egypt/egypt.html);

3\. [ncc](http://students.ceid.upatras.gr/~sxanth/ncc)。


## 扩展阅读

1\. IBM developerworks 上[M. Tim Jones 专栏](http://www.ibm.com/developerworks/cn/linux/theme/mtj)及[mtjones的主页](http://www.mtjones.com)；

2\. [Graphviz各大组件（dot等）工具](http://www.graphviz.org/Documentation.php)相关文档；

3\. GCC实用工具[addr2line](http://www.linuxcommand.org/man_pages/addr2line1.html)说明；

4\.  陈硕的博文[“用CodeViz绘制函数调用关系图”](http://blog.csdn.net/solstice/article/details/486788)；


## 参考文献

1\. M.Tim Jones的文章[用Graphviz可视化函数调用](http://www.ibm.com/developerworks/cn/linux/l-graphvis)。

