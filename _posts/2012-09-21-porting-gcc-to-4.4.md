--- 
layout: post
title: 针对GCC 4.4版本的C/C++软件移植指南
date: 2012-09-21
categories:
  - 技术
tags:
  - GCC4.4
  - Porting
  - C/C++
excerpt: <dl class="nr">
 <dt><img src="/img/article/gcc.png"/> </dt>
 <dd>
 <p>本文是Porting to GCC系列的第二篇。主要介绍GCC 4.4版本的特性及C/C++软件移植相关的问题。</p>
 </dd> </dl>
---

本文是Porting to GCC系列的第二篇。主要介绍GCC 4.4版本的特性及C/C++软件移植相关的问题。

## 扩展阅读
如果你希望更加深入的了解GCC相关特性、体系及编程等，可参考以下的材料：

1.    各类官方文档
2.    The Definitive Guide to GCC（William von Hagen著）：全面介绍GCC相关内容，包括auto*工具链使用及语言特性的扩展等。
3.    Unix to Linux Porting (Alfredo mendoza等著)：这是*nix软件移植开发最好的指南。

## GCC 4.4版本的变化

### C语言相关问题

#### 预处理器条件都被执行

当用`#elif`时候，现在其参数将被执行，即使前面的`#if`或`#elif`条件执行结果为非0(onoe-zero）。这样做的目的是确保，它们（宏）是有效的常量表达式。示例代码，如下：

<pre class="prettyprint linenums">
#if 1
#elif
#endif
</pre>

在新版本下，会产生以下错误诊断：
<pre class="prettyprint">
error: #elif with no expression
</pre>

修正手段：要么使用不带参数`#else`，要么为`#elif`提供常量表达式。

### 更加严格的“别名”要求

当优化代码时，GCC会对很多“类型双关type-punning”情况，发出告警。例如，
<pre class="prettyprint linenums">
struct A 
{ 
  char data[14];
  int i; 
};

void foo()
{
  char buf[sizeof(struct A)];
  ((struct A*)buf)->i = 4;
}
</pre>

在新版本下，会产生以下错误诊断：
<pre class="prettyprint linenums">
warning: dereferencing type-punned pointer will break strict-aliasing rules
</pre>

可以使用`-fno-strict-aliasing`为变通方案或使用`-Wno-strict-aliasing`忽略此类问题。要修正问题，可以通过`相等类型的指针`访问结构体，用一个union，用memcpy或（如果是C++）用`new`操作替换。

### C++语言相关问题

#### 头文件依赖调整

针对头文件依赖，一些标准C++库include文件已被优化，仅include最小可能数量的附加文件。因此，使用了`std::printf`而没有include &lt;cstdio&gt; 的C++程序，或使用了`uint32_t`而没有include &lt;stdint.h&gt; 的，将不再编译，而是报错。

具体细节，如下:

文件&lt;cstdio&gt;将不再作为&lt;string&gt;,&lt;ios&gt;,&lt;iomanip&gt;,&lt;streambuf&gt;,或&lt;locale&gt;的一部分被include。

文件&lt;stdint.h&gt;将不再作为&lt;string&gt;或&lt;ios&gt;的一部分被include.


#### 限制null终止（null-terminated）序列的函数

一些C++库include已被修改，来替换C库函数的重载，以提高常量的正确性（const-correctness）：接收const char*，同时返回const char*的函数。相关变更，如下表所示：

<table title="" border="1">
  <tbody><tr>
    <th>头文件</th>
    <th>函数</th>
  </tr>
  <tr>
    <td>&lt;cstring&gt;</td>
    <td>
      <code>strchr</code>,
      <code>strpbrk</code>,
      <code>strrchr</code>,
      <code>strstr</code>,
      <code>memchr</code>
    </td>
  </tr>
  <tr>
    <td>&lt;cwchar&gt;</td>
    <td>
      <code>wcschr</code>
      <code>wcspbrk</code>,
      <code>wcsrchr</code>,
      <code>wcsstr</code>,
      <code>wmemchr</code>
    </td>
  </tr>
</tbody></table>

应用示例，如下：

<pre class="prettyprint linenums">
#include &lt;cstring&gt;

const char* str1;
char* str2 = strchr(str1, 'a');
</pre>

编译时，会报如下错误：
<pre class="prettyprint">
error: invalid conversion from ‘const char*’ to ‘char*’
</pre>

修正方式，如下：
<pre class="prettyprint linenums">
#include &lt;cstring&gt;

const char* str1;
`const char* str2` = strchr(str1, 'a');
</pre>

#### 初始化调整

GCC 4.4默认不再支持如下的代码：
<pre class="prettyprint linenums">
struct A { virtual ~A (); };

struct B : public A { int i; };

struct C
{ 
  const B a; 
  C() { bar(&a); } 
  void bar(const B*); 
};
</pre>

编译这个代码会报以下错误：
<pre class="prettyprint">
In constructor 'C::C()':
error: uninitialized member 'C::a' with 'const' type 'const B'
</pre>

用`成员初始化列表`初始化成员即可解决相关问题。具体，如下：
<pre class="prettyprint linenums">
C(): a(B()) { bar(&a); } 
</pre>

