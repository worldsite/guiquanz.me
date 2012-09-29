--- 
layout: post
title: 针对GCC 4.7版本的C/C++软件移植指南
date: 2012-09-21
categories:
  - 技术
tags:
  - GCC4.7
  - Porting
  - C/C++
excerpt: <dl class="nr">
 <dt><img src="/img/article/gcc.png"/> </dt>
 <dd>
 <p>本文是Porting to GCC系列的第四篇。主要介绍GCC 4.7版本的特性及C/C++软件移植相关的问题。</p>
 </dd> </dl>
---
## 缘由

本文是Porting to GCC系列的第四篇。主要介绍GCC 4.7版本的特性及C/C++软件移植相关的问题。

## 扩展阅读
如果你希望更加深入的了解GCC相关特性、体系及编程等，可参考以下的材料：

1.    各类官方文档
2.    The Definitive Guide to GCC（William von Hagen著）：全面介绍GCC相关内容，包括auto*工具链使用及语言特性的扩展等。
3.    Unix to Linux Porting (Alfredo mendoza等著)：这是*nix软件移植开发最好的指南。


## 常规问题

### 在连接（linking）时，使用了无效的flag

前期版本的gcc/g++/gfortran等在完全无效的选项上不会产生告警或错误（在命令行中，如果没有任何东西被编译，仅`连接`被执行）。新版本
GCC 4.7针对这些问题，进行了相应的强化处理。例如，
<pre class="prettyprint linenums">
gcc -Wl -o foo foo.o -mflat_namespace
</pre>
执以上命令会报如下的错误：
<pre class="prettyprint">
error: unrecognized command line option ‘-Wl’
error: unrecognized command line option ‘-mflat_namespace’
</pre>

需要去掉命令行中无效的选项或用有效的选项替换。

## C++语言问题

### 头文件依赖调整

为了去掉“名字空间污染”，很多标准C++库include文件已被修改，不再include `<unistd.h>`。

因此，很多使用了truncate, sleep或pipe而没有include `<unistd.h>`的C++程序，在编译时会报如下的错误：
<pre class="prettyprint">
error: ‘truncate’ was not declared in this scope
error: ‘sleep’ was not declared in this scope
error: ‘pipe’ was not declared in this scope
error: there are no arguments to 'offsetof' that depend on a template
parameter, so a declaration of 'offsetof' must be available
</pre>

只要include `<unistd.h>`文件，即可解决此问题。

### 线程支持的检测

应该使用`POSIX`规范的`_REENTRANT`宏，而不是私有的GCC-implementation-space宏（如`_GLIBCXX_HAS_GTHREADS`等），在编译时检测线程并发的支持。

### 名字查询调整

C++编译器不再执行一些“特别不合格的”（extra unqualified）查询，以前版本是会执行此操作的，也就是说（namely）依赖基类边界查询和不合格的模板函数查询。

依赖于编译器之前版本行为的C++程序将不再被编译。如，
<pre class="prettyprint linenums">
template&lt;typename T&gt;
int t(T i)
{ return f(i); }

int
f(int i)
{ return i; }

int
main()
{
  return t(1);
}
</pre>

将产生以下的诊断信息：

<pre class="prettyprint">
In instantiation of ‘int t(T) [with T = int]’
  required from here
  error: ‘f’ was not declared in this scope, and no declarations were found by argument-dependent lookup at the point of instantiation [-fpermissive]
  note: ‘int f(int)’ declared here, later in the translation unit
</pre>

修正问题的手段： 确保`f函数`在`t`中第一次使用之前已经声明。例如，
<pre class="prettyprint linenums">
int
f(int i)
{ return i; }

template&lt;typename T&gt;
int t(T i)
{ return f(i); }

int
main()
{
  return t(1);
}
</pre>

这段代码在使用`-fpermissive`时，可正常执行（临时处理方案）。

### 嵌套边界/范围中变量名的重声明的检测

C++编译器不再允许，在一些嵌套的边界中声明相同名称的标识符。例如，
<pre class="prettyprint linenums">
void f(int);

int main()
{
  for (int i=0;;++i) {
    int i=5;
    f(i);
  }
  return 0;
}
</pre>

在编译时，会报错误：
<pre class="prettyprint linenums">
error: redeclaration of ‘int i’
error: ‘int i’ previously declared here
</pre>

修改办法：修改一个变量i的名字，以区分不同的标识符。

### 用户自定义的“字面值”和“空格”

处于ISO C11模式std={c++11,c++0x,gnu++11,gnu++0x}下的C++编译器，支持用户自定义“字面值”，这与合法的ISO C++03代码不兼容。尤其，现在必须在字符串字面值之后和一些有效的用户自定义字面值之前增加“空格”。以有效的ISO C++03代码为例：
<pre class="prettyprint linenums">
const char *p = "foobar"__TIME__;
</pre>

在C++03中, 宏`__TIME__`展开为一个“字符串字面值”，被拼接到另一个字符串中。而在C++11中`__TIME__`将不被展开，相反会查询操作符`"" __TIME__`，结果会产生以下的诊断：

<pre class="prettyprint">
 error: unable to find string literal operator
 ‘operator"" __TIME__’
</pre>

这个规则适用于任何没有`空格`而直接被一些`宏`跟在后面的`字符串字面值`。在字符串和宏名称之间，增加一些空格，即可解决此类问题。

### 模板实例的可见性

模板实例的ELF符号可见性，完全被模板参数的可见性约束。

因此，使用`-fvisibility=hidden`编译的用户需要注意从库头文件中include的类型的可见性。如果，头文件没有明确控制符号可见性（如标准C++库那样），从这些头文件中include类型将被屏蔽（hidden），同时，使用这些类型作为模板参数的实例化也将被屏蔽。例如，
（The ELF symbol visibility of a template instantiation is now properly constrained by the visibility of its template arguments. As a result, users that compile with -fvisibility=hidden should be aware of the visibility of types #included from library headers; if the header does not explicitly control symbol visibility (as the standard C++ library does) types from those headers will be hidden, and so instantiations that use those types as template arguments will also be hidden.）

<pre class="prettyprint linenums">
#include &lt;vector&gt;               // template std::vector has default visibility
#include &lt;ctime&gt;                // struct tm has hidden visibility
template class std::vector&lt;tm&gt;; // instantiation has hidden visibility
</pre>

同样的，头文件中只有使用外部`明确模板实例化`的代码，可以感知这个变化。调整一个库的头文件foo.h可见性的一种方法是，用`-I`导入foo.h所在路径,创建一个向前的头文件。如，
（Most likely only code that uses extern explicit template instantiation in headers will notice this change. One approach to adjusting the visibility of a library header foo.h is to create a forwarding header on the -I include path consisting of）

<pre class="prettyprint linenums">
#pragma GCC visibility push(default)
#include_next &lt;foo.h&gt;
#pragma GCC visibility push
</pre>

