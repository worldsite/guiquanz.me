--- 
layout: post
title: 针对GCC 4.3版本的C/C++软件移植指南
date: 2012-09-21
categories:
  - 技术
tags:
  - GCC4.3
  - Porting
  - C/C++
excerpt: <dl class="nr">
 <dt><img src="/img/article/gcc.png"/> </dt>
 <dd>
 <p>2012年5月份花了大概一周的时间，在Linux PPC平台GCC 4.4环境下，将前公司的业务管理系统进行了移植开发及测试。涉及变更的内容不少（之前已针对GCC 4.1进行过移植），但多数都在以下FSF官方GCC变更Porting to GCC系列文档列举的范围之内，为了便于语言不通的朋友阅读，统一进行了翻译、编辑整理。我将4.6和4.7版本相关内容也加进来，按照系列文章的形式进行编排。GCC是一个工具集的总称，这里只关注C/C++相关内容。</p>
 </dd> </dl>
---
## 缘由

2012年5月份花了大概一周的时间，在Linux PPC平台GCC 4.4环境下，将前公司的业务管理系统进行了移植开发及测试。涉及变更的内容不少（之前已针对GCC 4.1进行过移植），但多数都在以下FSF官方GCC变更Porting to GCC系列文档列举的范围之内，为了便于语言不通的朋友阅读，统一进行了翻译、编辑整理。我将4.6和4.7版本相关内容也加进来，按照系列文章的形式进行编排。GCC是一个工具集的总称，这里只关注C/C++相关内容：

1.    [GCC 4.3 Release Series Porting to the New Tools](http://gcc.gnu.org/gcc-4.3/porting_to.html)
2.    [GCC 4.4 Release Series Porting to the New Tools](http://gcc.gnu.org/gcc-4.4/porting_to.html)
3.    [Porting to GCC 4.6](http://gcc.gnu.org/gcc-4.6/porting_to.html)
4.    [Porting to GCC 4.7](http://gcc.gnu.org/gcc-4.7/porting_to.html)

## 扩展阅读
如果你希望更加深入的了解GCC相关特性、体系及编程等，可参考以下的材料：

1.    各类官方文档
2.    The Definitive Guide to GCC（William von Hagen著）：全面介绍GCC相关内容，包括auto*工具链使用及语言特性的扩展等。
3.    Unix to Linux Porting (Alfredo mendoza等著)：这是*nix软件移植开发最好的指南。

## GCC 4.3版本的变化

### C语言相关问题

#### extern inline语义的变化

当通过`-stdc99`或`-std=gnu99`编译时，改变了extern inline的语义。GCC 4.3遵循ISO C99规范，其中extern inline是和GNU的extern inline是完全不同的东西。以下的代码，如果用-std=c99编译，
<pre class="prettyprint linenums">
extern inline int
foo()
{ return 5; }
</pre>

其结果是“foo的一个函数定义，将在随后的对象文件中生成”，然而之前版本这里结果是none。如果用C99的方言（如，gnu99）,编译使用此扩展的文件，会报以下的错误：

> multiple definition of `foo'

> first defined here 

> When linking together multiple object files.


如果希望继续使用老版本的GNU的extern inline，

(1). 可以用extern inline `__attribute__((__gnu_inline__))`。使用此属性时，需要定义`#ifdef __GNUC_STDC_INLINE__`开关，inline拥有C99行为时会定义此宏。

(2). 使用`-fgnu89-inline`选项编译。

预处理之后的代码，如下：
<pre class="prettyprint linenums">
extern inline __attribute__((__gnu_inline__)) int
foo()
{ return 5; }
</pre>

#### 新增的告警

主要修改，主要针对`-W变换（-Wconversion）`的。同时，为了更好的识别、分离存在问题的代码，对底层进行了优化。所以以前通过-Wuninitialized, -Wstrict-aliasing , -Wunused-function, -Wunused-variable符号编译的没有告警的代码，可能会产生新的告警。注意， -Wall包括其他很多的标记。

虽然新增告警不会造成编译失败，但经常将`-Wall`和`-Werror`连用，新的告警会产生新的错误。

作为变通，直到新的告警被修复之前去掉`-Werror`，或者可以用`-Wno-变换（-Wno-conversion）`过滤告警。

### C++相关问题

#### 头文件依赖清除

针对头文件依赖，很多标准C++库include文件已被优化，仅include最小可能数量的附加文件。因此，很多使用了`std::memcpy`而没有include &lt;cstring&gt; 的C++程序，或使用了`std::auto_ptr`而没有include &lt;memory&gt; 的，将不再编译，而是报错。通常会产生类似以下形式的错误：

> error: 'strcmp' was not declared in this scope

涉及到的关系表，如下:
<table title="" border="1">
  <tbody><tr>
    <th> 如果此内容找不到 </th>
    <th> 请include此头文件 </th>
  </tr>
  <tr>
    <td>find, for_each, sort</td>
    <td>&lt;algorithm&gt;</td>
  </tr>
  <tr>
    <td>ostream_iterator, istream_iterator</td>
    <td>&lt;iterator&gt;</td>
  </tr>
  <tr>
    <td>auto_ptr</td>
    <td>&lt;memory&gt;</td>
  </tr>
  <tr>
    <td>typeid</td>
    <td>&lt;typeinfo&gt;</td>
  </tr>
  <tr>
    <td>isalnum, toupper</td>
    <td>&lt;cctype&gt;</td>
  </tr>
  <tr>
    <td>INT_MIN, INT_MAX, RAND_MAX</td>
    <td>&lt;climits&gt;</td>
  </tr>
  <tr>
    <td>printf</td>
    <td>&lt;cstdio&gt;</td>
  </tr>
  <tr>
    <td>atoi, free, rand, exit</td>
    <td>&lt;cstdlib&gt;</td>
  </tr>
  <tr>
    <td>EXIT_FAILURE</td>
    <td>&lt;cstdlib&gt;</td>
  </tr>
  <tr>
    <td>strcmp, strdup, strcpy, memcpy</td>
    <td>&lt;cstring&gt;</td>
  </tr>
	</tbody>
</table>

#### 去掉了Pre-ISO（在ISO标准发布之前，定义的）头文件 

很多“过时的/落伍的”以及“不推荐使用的”头文件已被删除。具体内容，如下表：

<table title="" border="1">
  <tbody><tr>
    <th> 如果此内容找不到 </th>
    <th> 请include此头文件 </th>
  </tr>
  <tr>
    <td>&lt;algobase.h&gt;</td>
    <td>&lt;algorithm&gt;</td>
  </tr>
  <tr>
    <td>&lt;algo.h&gt;</td>
    <td>&lt;algorithm&gt;</td>
  </tr>
  <tr>
    <td>&lt;alloc.h&gt;</td>
    <td>&lt;memory&gt;</td>
  </tr>
  <tr>
    <td>&lt;bvector.h&gt;</td>
    <td>&lt;vector&gt;</td>
  </tr>
  <tr>
    <td>&lt;complex.h&gt;</td>
    <td>&lt;complex&gt;</td>
  </tr>
  <tr>
    <td>&lt;defalloc.h&gt;</td>
    <td>&lt;memory&gt;</td>
  </tr>
  <tr>
    <td>&lt;deque.h&gt;</td>
    <td>&lt;deque&gt;</td>
  </tr>
  <tr>
    <td>&lt;fstream.h&gt;</td>
    <td>&lt;fstream&gt;</td>
  </tr>
  <tr>
    <td>&lt;function.h&gt;</td>
    <td>&lt;functional&gt;</td>
  </tr>
  <tr>
    <td>&lt;hash_map.h&gt;</td>
    <td>&lt;tr1/unordered_map&gt;</td>
  </tr>
  <tr>
    <td>&lt;hashtable.h&gt;</td>
    <td>&lt;tr1/unordered_map&gt; or &lt;tr1/unordered_set&gt;</td>
  </tr>
  <tr>
    <td>&lt;heap.h&gt;</td>
    <td>&lt;queue&gt;</td>
  </tr>
  <tr>
    <td>&lt;iomanip.h&gt;</td>
    <td>&lt;iomanip&gt;</td>
  </tr>
  <tr>
    <td>&lt;iostream.h&gt;</td>
    <td>&lt;iostream&gt;</td>
  </tr>
  <tr>
    <td>&lt;istream.h&gt;</td>
    <td>&lt;istream&gt;</td>
  </tr>
  <tr>
    <td>&lt;iterator.h&gt;</td>
    <td>&lt;iterator&gt;</td>
  </tr>
  <tr>
    <td>&lt;list.h&gt;</td>
    <td>&lt;list&gt;</td>
  </tr>
  <tr>
    <td>&lt;map.h&gt;</td>
    <td>&lt;map&gt;</td>
  </tr>
  <tr>
    <td>&lt;multimap.h&gt;</td>
    <td>&lt;map&gt;</td>
  </tr>
  <tr>
    <td>&lt;multiset.h&gt;</td>
    <td>&lt;set&gt;</td>
  </tr>
  <tr>
    <td>&lt;new.h&gt;</td>
    <td>&lt;new&gt;</td>
  </tr>
  <tr>
    <td>&lt;ostream.h&gt;</td>
    <td>&lt;ostream&gt;</td>
  </tr>
  <tr>
    <td>&lt;pair.h&gt;</td>
    <td>&lt;utility&gt;</td>
  </tr>
  <tr>
    <td>&lt;queue.h&gt;</td>
    <td>&lt;queue&gt;</td>
  </tr>
  <tr>
    <td>&lt;rope.h&gt;</td>
    <td>&lt;ext/rope&gt;</td>
  </tr>
  <tr>
    <td>&lt;set.h&gt;</td>
    <td>&lt;set&gt;</td>
  </tr>
  <tr>
    <td>&lt;slist.h&gt;</td>
    <td>&lt;ext/slist&gt;</td>
  </tr>
  <tr>
    <td>&lt;stack.h&gt;</td>
    <td>&lt;stack&gt;</td>
  </tr>
  <tr>
    <td>&lt;streambuf.h&gt;</td>
    <td>&lt;streambuf&gt;</td>
  </tr>
  <tr>
    <td>&lt;stream.h&gt;</td>
    <td>&lt;iostream&gt;</td>
  </tr>
  <tr>
    <td>&lt;tempbuf.h&gt;</td>
    <td>&lt;ext/memory&gt;</td>
  </tr>
  <tr>
    <td>&lt;tree.h&gt;</td>
    <td>&lt;ext/rb_tree&gt; or &lt;ext/pb_ds/assoc_container.hpp&gt;</td>
  </tr>
  <tr>
    <td>&lt;vector.h&gt;</td>
    <td>&lt;vector&gt;</td>
  </tr>
</tbody></table>

可以进一步了解[其他](http://gcc.gnu.org/onlinedocs/libstdc++/manual/bk01pt01ch03s02.html)相关的头文件。

*应用示例*
<pre class="prettyprint linenums">
#include &lt;iostream.h&gt;

int main()
{
  cout << "I'm too old" << endl;
  return 0;
}
</pre>

通过`4.3之前的GCC`版本编译，会产生如下告警：
<pre class="prettyprint">
warning: #warning This file includes at least one deprecated or antiquated header. 
</pre>
请酌情使用`C++标准文档 17.4.1.2节`（这个可能会有变化）罗列的`32`个头文件中的一个。比如，用<X>替换原来的<X.h>头文件或用<iostream>替换<iostream.h>。如果需要禁用此告警，请使用`-Wno-deprecated`编译选项。

用`4.3版本`编译，会报错：
<pre class="prettyprint">
error: iostream.h: No such file or directory
In function 'int main()':
6: error: 'cout' was not declared in this scope
6: error: 'endl' was not declared in this scope
</pre>

修正办法，如下：

<pre class="prettyprint linenums">
#include &lt;iostream&gt;
using namespace std;

int main()
{
  cout << "I work again" << endl;
  return 0;
}
</pre>

#### 名字查询变更

GCC默认不再支持以下代码：
<pre class="prettyprint linenums">
template &lt;class _Tp&gt; class auto_ptr {};
template &lt;class _Tp&gt;
struct counted_ptr
{
  auto_ptr<_Tp> auto_ptr();
};
</pre>
这个代码会产生一个错误诊断：

<pre class="prettyprint">
error: declaration of 'auto_ptr<_Tp> counted_ptr<_Tp>::auto_ptr()'
error: changes meaning of 'auto_ptr' from 'class auto_ptr<_Tp>'
</pre>

需要限定对auto\_ptr结构的引用或修改成员函数名去除二义性。如，
<pre class="prettyprint linenums">
template &lt;class _Tp&gt; class auto_ptr {};
template &lt;class _Tp&gt;
struct counted_ptr
{
  ::auto_ptr<_Tp> auto_ptr();
};
</pre>

另外，在代码修复之前可以用`-fpermissive`选项，将错误转为告警（作为变通）。注意，此时有些情况下“名字查询”将采用非标准的配置。

#### 冗余函数参数

C/C++中的冗余函数都将统一当作“错误”处理。如，
<pre class="prettyprint linenums">
void foo(int w, int w);
</pre>

在新版本下，会报如下的错误：
<pre class="prettyprint">
error: multiple parameters named 'w'
</pre>
通过修改一个参数，令命名唯一，即可解决此问题。如，
<pre class="prettyprint linenums">
void foo(int w, int w2);
</pre>

#### 更加严格的main函数签名

GCC 4.3强制必须保证`main`函数两个参数中，第一个参数为`int`。针对以下代码：
<pre class="prettyprint linenums">
int main(unsigned int m, char** c) 
{ return 0; }
</pre>

会报错：
<pre class="prettyprint linenums">
error: first argument of 'int main(unsigned int, char**)' should be 'int'
</pre>

将第一个参数由`unsigned int`改为`int`，方能修正错误：
<pre class="prettyprint linenums">
int main(int m, char** c) 
{ return 0; }
</pre>

#### 明确的template特殊化，不能拥有存储class

殊化的模板（template）不能明确指定存储class，以及相同的存储作为主template。这是根据`ISO C++ Core Defect Report 605`修改的行为。具体，如下：

<pre class="prettyprint linenums">
template&lt;typename T&gt;
  static void foo();

template<>
  static void foo<void>();  
</pre>

编译代码会报，以下的错误：
<pre class="prettyprint">
error: explicit template specialization cannot have a storage class
</pre>

`外部说明符（指示符）`也存在此问题。修正方法：去掉“特殊化”上的“存储说明符”即可。具体，如下：
<pre class="prettyprint linenums">
template&lt;typename T&gt;
  static void foo();

template<>
  void foo<void>();
</pre>

