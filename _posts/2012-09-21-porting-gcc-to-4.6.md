--- 
layout: post
title: 针对GCC 4.6版本的C/C++软件移植指南
date: 2012-09-21
categories:
  - 技术
tags:
  - GCC4.6
  - Porting
  - C/C++
excerpt: <dl class="nr">
 <dt><img src="/img/article/gcc.png"/> </dt>
 <dd>
 <p>本文是Porting to GCC系列的第三篇。主要介绍GCC 4.6版本的特性及C/C++软件移植相关的问题。</p>
 </dd> </dl>
---

本文是Porting to GCC系列的第三篇。主要介绍GCC 4.6版本的特性及C/C++软件移植相关的问题。

## 扩展阅读
如果你希望更加深入的了解GCC相关特性、体系及编程等，可参考以下的材料：

1.    各类官方文档
2.    The Definitive Guide to GCC（William von Hagen著）：全面介绍GCC相关内容，包括auto*工具链使用及语言特性的扩展等。
3.    Unix to Linux Porting (Alfredo mendoza等著)：这是*nix软件移植开发最好的指南。

## GCC 4.4版本的变化

### C语言相关问题

#### 针对未使用的`变量`和`参数`提供新的告警

`-Wall`的行为已发生变化，包括了新增的`-Wunused-but-set-variable`和`-Wunused-but-set-parameter`（和 -Wall -Wextra)。这可能会对一些代码产生告警。

例如，编译以下代码
<pre class="prettyprint linenums">
void fn (void)
{
  int foo;
  foo = bar ();  /* foo is never used.  */
}
</pre>

会产生以下告警诊断：
<pre class="prettyprint">
warning: variable "foo" set but not used [-Wunused-but-set-variable]
</pre>

虽然新增告警不会造成编译失败，但经常将`-Wall`和`-Werror`连用，新的告警会产生新的错误。

修正手段： 确认是否可以去掉未使用的变量或参数而不对结果或周围代码的逻辑造成影响。如果不能直接去掉，请用`__attribute__((__unused__))`进行标记。

作为变通，直到新的告警被修复之前，通过增加`-Wno-error=unused-but-set-variable`，或者`-Wno-error=unused-but-set-parameter`过滤告警。


### C++语言相关问题

#### 头文件依赖调整

很多标准C++头文件已被修改，不再通过include &lt;cstddef&gt;来获取`std`命名空间边界版本的`size_t`和`ptrdiff_t`。

因此，使用了宏`NULL`或`offsetof`而没有include `<cstddef>`的C++程序，在编译时，会报类似以下形式的错误：
<pre class="prettyprint">
error: 'ptrdiff_t' does not name a type
error: 'size_t' has not been declared
error: 'NULL' was not declared in this scope
error: there are no arguments to 'offsetof' that depend on a template
parameter, so a declaration of 'offsetof' must be available
</pre>

只要include `<cstddef>`即可解决问题。

