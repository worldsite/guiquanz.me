---
layout: post
title: RE2，C++正则表达式库实战
date: 2013-08-11
categories:
  - 技术
tags:
  - RE2
---
[![RE2](/img/article/2013-08/11-01.png)](http://code.google.com/p/re2/wiki/Install)

[RE2](http://code.google.com/p/re2/wiki/Install)是,一个`高效、原则性的正则表达式库`，由`Rob Pike`和`Russ Cox`两位来自google的大牛用`C++`实现。他俩同时也是`Go`语言的主导者。Go语言中的`regexp`正则表达式包，也是RE2的Go实现。

RE2是，一个`快速`、`安全`，`线程友好`，PCRE、PERL和Python等回溯正则表达式引擎（backtracking regular expression engine）的一个替代品。RE2支持Linux和绝大多数的Unix平台，但不支持Windows（如果有必要，你可以自己hack）。


## 目录

- [RE2的特点](#RE2%E7%9A%84%E7%89%B9%E7%82%B9)
- [玩转RE2](#%E7%8E%A9%E8%BD%ACRE2)
- [“回溯”与“非回溯”的区别](#%E2%80%9C%E5%9B%9E%E6%BA%AF%E2%80%9D%E4%B8%8E%E2%80%9C%E9%9D%9E%E5%9B%9E%E6%BA%AF%E2%80%9D%E7%9A%84%E5%8C%BA%E5%88%AB)
- [RE2的各种包装](#RE2%E7%9A%84%E5%90%84%E7%A7%8D%E5%8C%85%E8%A3%85)
- [RE2支持的语法](#RE2%E6%94%AF%E6%8C%81%E7%9A%84%E8%AF%AD%E6%B3%95)


## RE2的特点

`回溯引擎`（Backtracking engine）通常是典型的完整的功能和便捷的语法糖，但是`即使很小的输入`都可能强制进入`指数级时间`处理场景。RE2应用`自动机理论理论`，来保证在一个尺寸的输入上正则表达式搜索运行于一个时间线。RE2实现了`内存限制`，所以搜索可以被制约在一个固定大小的内存。RE2被设计为使用一个很小的`固定C++堆栈足迹`，无论它必须处理的输入或正则表达式是什么。从而RE2在`多线程环境`非常有用，当`线程栈`不能武断的增大时。

当输入（数据集）很大时，RE2通常比`回溯引擎`快很多。它采用`自动机理论`，实施别的引擎无法进行的优化。

不同于绝大多数`基于自动机`的引擎，RE2实现了几乎所有Perl和PCRE特点，和语法糖。它找到`最左-优先`（leftmost-first）匹配，同时匹配Perl可能匹配的，并且能返回`子匹配`信息。最明显的`例外`是，RE2`去掉了`对`反向引用`（backreferences）和一般性`零-宽度断言`（zero-width assertion）的支持，因为无法高效实现。

为了相对简单语法的使用者，RE2，有一个`POSIX`模式，仅接受`POSIX egrep算子`，实现`最左-最长`整体匹配（leftmost-longest overall matching）。

[![xkcd](/img/article/2013-08/11-02.png)](http://xkcd.com/208)

¹ Technical note: there's a difference between submatches and backreferences. Submatches let you find out what certain subexpressions matched after the match is over, so that you can find out, after matching dogcat against (cat|dog)(cat|dog), that \1 is dog and \2 is cat. Backreferences let you use those subexpressions during the match, so that (cat|dog)\1 matches catcat and dogdog but not catdog or dogcat.

RE2支持`子匹配萃取`（submatch extraction），但是不支持`反向引用`（backreferences）。

如果你必须要`反向引用`和`一般性断言`，而RE2不支持，那么你可以看一下`irregexp`，Google Chrome的正则表达式引擎。

## 玩转RE2

### 安装

你可以下载[发行版的代码包](http://code.google.com/p/re2/downloads/list)，然后解压进行安装。这里介绍，另一种安装方式：

需要安装[Mercurial SCM](mercurial.selenic.com)和C++编译器（g++的克隆）：

下载代码，并进行安装：
    
    hg clone http://re2.googlecode.com/hg re2
    cd re2
    make test
    make testinstall
    sudo make install
    
__在BSD系统, 使用`gmake`替换`make`__

### 使用RE2库

使用RE2库开发C++应用，需要在代码中包含`re2/re2.h`头文件，链接时增加 `-lre2`以及`-lpthread`（多线环境使用）选项。


### 语法

在`POSIX模式`，RE@接受标准POSIX (egrep)语法正则表达式。在`Perl`模式，RE2接受大部分Perl操作符。唯一例外的是，那些要求`回溯`（潜在需要指数级的运行时）实现的部分。其中，包括`反向引用`（子匹配，还是支持的）和`一般性断言`。RE2,默认为Perl模式。


### C++ 高级接口

这里包括两个基本的操作：

* RE2::FullMatch: 要求regexp表达式匹配整个输入文本。
* RE2::PartialMatch: 在输入文本中寻找一个子匹配。在POSIX模式，返回`最左-最长`匹配，Perl模式也是相同的匹配。

例如，
    
    vi re2_high_interface_test.cc
    
<pre class="prettyprint linenums">
#include &lt;re2/re2.h&gt;
#include &lt;iostream&gt;
#include &lt;assert.h&gt;

int
main(void)
{
    assert(RE2::FullMatch("hello", "h.*o"));
    assert(!RE2::FullMatch("hello", "e"));

    assert(RE2::PartialMatch("hello", "h.*o"));
    assert(RE2::PartialMatch("hello", "e"));

    std::cout << "Ok" << std::endl;
    return 0;
}
</pre>

编译程序：
    
    g++ -o re2_high_interface_test re2_high_interface_test.cc -lre2
        
执行re2\_high\_interface\_test，程序正常运行，显示结果`Ok`。

### 子匹配萃取

两个匹配函数，都支持附加参数，来指定`子匹配`。此参数可以是一个`字符串`或一个`整数类型`或`StringPiece`类型。__一个`StringPiece`是一个指向原始输入的`指针`,和一个字符串的长度计数__。有点类似一个`string`，但是有自己的存储。和使用指针一样，当使用`StringPiece`时，你必须小心谨慎，原始文本已被删除或不在相同的边界时，不能使用。

示例：
    
    vi re2_submatch_ex_test.cc
    
<pre class="prettyprint linenums">
#include &lt;re2/re2.h&gt;
#include &lt;iostream&gt;
#include &lt;assert.h&gt;

int
main(void)
{
    int i;
    std::string s;
    assert(RE2::FullMatch("ruby:1234", "(\\w+):(\\d+)", &s, &i));
    assert(s == "ruby");
    assert(i == 1234);

    // Fails: "ruby" cannot be parsed as an integer.
    assert(!RE2::FullMatch("ruby", "(.+)", &i));

    // Success; does not extract the number.
    assert(RE2::FullMatch("ruby:1234", "(\\w+):(\\d+)", &s));

    // Success; skips NULL argument.
    assert(RE2::FullMatch("ruby:1234", "(\\w+):(\\d+)", (void*)NULL, &i));

    // Fails: integer overflow keeps value from being stored in i.
    assert(!RE2::FullMatch("ruby:123456789123", "(\\w+):(\\d+)", &s, &i));

    std::cout << "Ok" << std::endl;
    return 0;
}
</pre>
    
    g++ -o re2_submatch_ex_test re2_submatch_ex_test.cc -lre2
  
### 预编译的正则表达式

上面的示例都是每次调用的时编译一次正则表达式。相反，你可以编译一次正则表达式，保存到一个RE2对象中，然后在每次调用时重用这个对象。

示例:
    
    vi re2_prec_re_test.cc
   
<pre class="prettyprint linenums">
#include &lt;re2/re2.h&gt;
#include &lt;iostream&gt;
#include &lt;assert.h&gt;

int
main(void)
{
    int i;
    std::string s;
    RE2 re("(\\w+):(\\d+)");
    assert(re.ok());  // compiled; if not, see re.error();

    assert(RE2::FullMatch("ruby:1234", re, &s, &i));
    assert(RE2::FullMatch("ruby:1234", re, &s));
    assert(RE2::FullMatch("ruby:1234", re, (void*)NULL, &i));
    assert(!RE2::FullMatch("ruby:123456789123", re, &s, &i));

    std::cout << "Ok" << std::endl;
    return 0;
}
</pre>
    
    g++ -o re2_prec_re_test re2_prec_re_test.cc -lre2


### 选项

RE2构造器还有`第二个可选参数`，可以用来改变RE2的默认选项。例如，预定义的`Quiet`选项，当正则表达式解析失败时，不打印错误消息：
    
    vi re2_options_test.cc
   
<pre class="prettyprint linenums">
#include &lt;re2/re2.h&gt;
#include &lt;iostream&gt;
#include &lt;assert.h&gt;

int
main(void)
{
    RE2 re("(ab", RE2::Quiet);  // don't write to stderr for parser failure
    assert(!re.ok());  // can check re.error() for details

    std::cout << "Ok" << std::endl;
    return 0;
}
</pre>

编译程序：
    
    g++ -o re2_options_test re2_options_test.cc -lre2

其他有用的预定义选项，是`Latin1` (禁用UTF-8)和`POSIX` (使用`POSIX语法`和`最左-最长`匹配)。

你可以定义自己的`RE2::Options`对象，然后配置它。所有的选项在`re2/re2.h`文件中。

### Unicode规范化

RE2操作Unicode的`码点`（code points）: 它没有试图进行规范化。例如，正则表达式`/ü/`(U+00FC, u和`分音符`)不匹配`"ü"`(U+0075 U+0308, `u`紧挨`结合分音符`)。规范化，是一个长期，参与的话题。最小的解决方案，如果你需要这样的匹配，是`在使用RE2之前的处理环节中同时规范化正则表达式和输入`。相关主题的更多细节，请参考[http://www.unicode.org/reports/tr15/](http://www.unicode.org/reports/tr15/)。

### 额外的技巧和窍门

RE2的高级应用技巧，如构造自己的`参数列表`，或将RE2作为`词法分析器`使用或`解析`十六进制、十进制和`C-基数`数字，请参考`re2.h`文件。


## “回溯”与“非回溯”的区别

__以下照片内容，源自“sregex: matching Perl 5 regexes on data streams”讲演文档.__

![回溯的意思](/img/article/2013-08/11-03.png)

![回溯方式实现](/img/article/2013-08/11-04.png)

![Robe Pike的算法](/img/article/2013-08/11-05.png)

![Thompson的构造的算法](/img/article/2013-08/11-06.png)


## RE2的各种包装

An 	`Inferno` wrapper is at [http://code.google.com/p/inferno-re2/](http://code.google.com/p/inferno-re2/).

A `Python` wrapper is at [http://github.com/facebook/pyre2/](http://github.com/facebook/pyre2/).

A `Ruby` wrapper is at [http://github.com/axic/rre2/](http://github.com/axic/rre2/).

An `Erlang` wrapper is at [http://github.com/tuncer/re2/](http://github.com/tuncer/re2/).

A `Perl` wrapper is at [http://search.cpan.org/~dgl/re-engine-RE2-0.05/lib/re/engine/RE2.pm](http://search.cpan.org/~dgl/re-engine-RE2-0.05/lib/re/engine/RE2.pm).

An `Eiffel` wrapper is at [http://sourceforge.net/projects/eiffelre2/](http://sourceforge.net/projects/eiffelre2/).


## RE2支持的语法

这里列出了RE2支持的正则表达式语法。同时，也列出了PCRE、PERL和VIM接受的语法。`蓝色`内容是，RE2不支持的语法。

<p><!-- GENERATED BY mksyntaxwiki.  DO NOT EDIT
 --> </p><p><table border="0" cellpadding="2" cellspacing="2"><tr><td></td></tr> <tr><td colspan="2"><b>Single characters:</b></td></tr> <tr><td><tt>.</tt></td><td>any character, including newline (s=true)</td></tr> <tr><td><tt>[xyz]</tt></td><td>character class</td></tr> <tr><td><tt>[^xyz]</tt></td><td>negated character class</td></tr> <tr><td><tt>\d</tt></td><td>Perl character class</td></tr> <tr><td><tt>\D</tt></td><td>negated Perl character class</td></tr> <tr><td><tt>[:alpha:]</tt></td><td>ASCII character class</td></tr> <tr><td><tt>[:^alpha:]</tt></td><td>negated ASCII character class</td></tr> <tr><td><tt>\pN</tt></td><td>Unicode character class (one-letter name)</td></tr> <tr><td><tt>\p{Greek}</tt></td><td>Unicode character class</td></tr> <tr><td><tt>\PN</tt></td><td>negated Unicode character class (one-letter name)</td></tr> <tr><td><tt>\P{Greek}</tt></td><td>negated Unicode character class</td></tr> <tr><td></td></tr> <tr><td colspan="2"><b>Composites:</b></td></tr> <tr><td><tt>xy</tt></td><td><tt>x</tt> followed by <tt>y</tt></td></tr> <tr><td><tt>x|y</tt></td><td><tt>x</tt> or <tt>y</tt> (prefer <tt>x</tt>)</td></tr> <tr><td></td></tr> <tr><td colspan="2"><b>Repetitions:</b></td></tr> <tr><td><tt>x*</tt></td><td>zero or more <tt>x</tt>, prefer more</td></tr> <tr><td><tt>x+</tt></td><td>one or more <tt>x</tt>, prefer more</td></tr> <tr><td><tt>x?</tt></td><td>zero or one <tt>x</tt>, prefer one</td></tr> <tr><td><tt>x{n,m}</tt></td><td><tt>n</tt> or <tt>n</tt>+1 or ... or <tt>m</tt> <tt>x</tt>, prefer more</td></tr> <tr><td><tt>x{n,}</tt></td><td><tt>n</tt> or more <tt>x</tt>, prefer more</td></tr> <tr><td><tt>x{n}</tt></td><td>exactly <tt>n</tt> <tt>x</tt></td></tr> <tr><td><tt>x*?</tt></td><td>zero or more <tt>x</tt>, prefer fewer</td></tr> <tr><td><tt>x+?</tt></td><td>one or more <tt>x</tt>, prefer fewer</td></tr> <tr><td><tt>x??</tt></td><td>zero or one <tt>x</tt>, prefer zero</td></tr> <tr><td><tt>x{n,m}?</tt></td><td><tt>n</tt> or <tt>n</tt>+1 or ... or <tt>m</tt> <tt>x</tt>, prefer fewer</td></tr> <tr><td><tt>x{n,}?</tt></td><td><tt>n</tt> or more <tt>x</tt>, prefer fewer</td></tr> <tr><td><tt>x{n}?</tt></td><td>exactly <tt>n</tt> <tt>x</tt></td></tr> <tr><td><font color="blue"><tt>x{}</tt></font></td><td>(≡ <tt>x*</tt>) <font size="1">(NOT SUPPORTED)</font> <font size="1">VIM</font></td></tr> <tr><td><font color="blue"><tt>x{-}</tt></font></td><td>(≡ <tt>x*?</tt>) <font size="1">(NOT SUPPORTED)</font> <font size="1">VIM</font></td></tr> <tr><td><font color="blue"><tt>x{-n}</tt></font></td><td>(≡ <tt>x{n}?</tt>) <font size="1">(NOT SUPPORTED)</font> <font size="1">VIM</font></td></tr> <tr><td><font color="blue"><tt>x=</tt></font></td><td>(≡ <tt>x?</tt>) <font size="1">(NOT SUPPORTED)</font> <font size="1">VIM</font></td></tr> <tr><td></td></tr> <tr><td colspan="2"><b>Possessive repetitions:</b></td></tr> <tr><td><font color="blue"><tt>x*+</tt></font></td><td>zero or more <tt>x</tt>, possessive <font size="1">(NOT SUPPORTED)</font></td></tr> <tr><td><font color="blue"><tt>x++</tt></font></td><td>one or more <tt>x</tt>, possessive <font size="1">(NOT SUPPORTED)</font></td></tr> <tr><td><font color="blue"><tt>x?+</tt></font></td><td>zero or one <tt>x</tt>, possessive <font size="1">(NOT SUPPORTED)</font></td></tr> <tr><td><font color="blue"><tt>x{n,m}+</tt></font></td><td><tt>n</tt> or ... or <tt>m</tt> <tt>x</tt>, possessive <font size="1">(NOT SUPPORTED)</font></td></tr> <tr><td><font color="blue"><tt>x{n,}+</tt></font></td><td><tt>n</tt> or more <tt>x</tt>, possessive <font size="1">(NOT SUPPORTED)</font></td></tr> <tr><td><font color="blue"><tt>x{n}+</tt></font></td><td>exactly <tt>n</tt> <tt>x</tt>, possessive <font size="1">(NOT SUPPORTED)</font></td></tr> <tr><td></td></tr> <tr><td colspan="2"><b>Grouping:</b></td></tr> <tr><td><tt>(re)</tt></td><td>numbered capturing group</td></tr> <tr><td><tt>(?P&lt;name&gt;re)</tt></td><td>named &amp; numbered capturing group</td></tr> <tr><td><font color="blue"><tt>(?&lt;name&gt;re)</tt></font></td><td>named &amp; numbered capturing group <font size="1">(NOT SUPPORTED)</font></td></tr> <tr><td><font color="blue"><tt>(?&#x27;name&#x27;re)</tt></font></td><td>named &amp; numbered capturing group <font size="1">(NOT SUPPORTED)</font></td></tr> <tr><td><tt>(?:re)</tt></td><td>non-capturing group</td></tr> <tr><td><tt>(?flags)</tt></td><td>set flags within current group; non-capturing</td></tr> <tr><td><tt>(?flags:re)</tt></td><td>set flags during re; non-capturing</td></tr> <tr><td><font color="blue"><tt>(?#text)</tt></font></td><td>comment <font size="1">(NOT SUPPORTED)</font></td></tr> <tr><td><font color="blue"><tt>(?|x|y|z)</tt></font></td><td>branch numbering reset <font size="1">(NOT SUPPORTED)</font></td></tr> <tr><td><font color="blue"><tt>(?&gt;re)</tt></font></td><td>possessive match of <tt>re</tt> <font size="1">(NOT SUPPORTED)</font></td></tr> <tr><td><font color="blue"><tt>re@&gt;</tt></font></td><td>possessive match of <tt>re</tt> <font size="1">(NOT SUPPORTED)</font> <font size="1">VIM</font></td></tr> <tr><td><font color="blue"><tt>%(re)</tt></font></td><td>non-capturing group <font size="1">(NOT SUPPORTED)</font> <font size="1">VIM</font></td></tr> <tr><td></td></tr> <tr><td colspan="2"><b>Flags:</b></td></tr> <tr><td><tt>i</tt></td><td>case-insensitive (default false)</td></tr> <tr><td><tt>m</tt></td><td>multi-line mode: ^ and $ match begin/end line in addition to begin/end text (default false)</td></tr> <tr><td><tt>s</tt></td><td>let <tt>.</tt> match <tt>\n</tt> (default false)</td></tr> <tr><td><tt>U</tt></td><td>ungreedy: swap meaning of <tt>x*</tt> and <tt>x*?</tt>, <tt>x+</tt> and <tt>x+?</tt>, etc (default false)</td></tr> <tr><td colspan="2">Flag syntax is <tt>xyz</tt> (set) or <tt>-xyz</tt> (clear) or <tt>xy-z</tt> (set <tt>xy</tt>, clear <tt>z</tt>).</td></tr> <tr><td></td></tr> <tr><td colspan="2"><b>Empty strings:</b></td></tr> <tr><td><tt>^</tt></td><td>at beginning of text or line (<tt>m</tt>=true)</td></tr> <tr><td><tt>$</tt></td><td>at end of text (like <tt>\z</tt> not <tt>\Z</tt>) or line (<tt>m</tt>=true)</td></tr> <tr><td><tt>\A</tt></td><td>at beginning of text</td></tr> <tr><td><tt>\b</tt></td><td>at word boundary (<tt>\w</tt> on one side and <tt>\W</tt>, <tt>\A</tt>, or <tt>\z</tt> on the other)</td></tr> <tr><td><tt>\B</tt></td><td>not a word boundary</td></tr> <tr><td><font color="blue"><tt>\G</tt></font></td><td>at beginning of subtext being searched <font size="1">(NOT SUPPORTED)</font> <font size="1">PCRE</font></td></tr> <tr><td><font color="blue"><tt>\G</tt></font></td><td>at end of last match <font size="1">(NOT SUPPORTED)</font> <font size="1">PERL</font></td></tr> <tr><td><font color="blue"><tt>\Z</tt></font></td><td>at end of text, or before newline at end of text <font size="1">(NOT SUPPORTED)</font></td></tr> <tr><td><tt>\z</tt></td><td>at end of text</td></tr> <tr><td><font color="blue"><tt>(?=re)</tt></font></td><td>before text matching <tt>re</tt> <font size="1">(NOT SUPPORTED)</font></td></tr> <tr><td><font color="blue"><tt>(?!re)</tt></font></td><td>before text not matching <tt>re</tt> <font size="1">(NOT SUPPORTED)</font></td></tr> <tr><td><font color="blue"><tt>(?&lt;=re)</tt></font></td><td>after text matching <tt>re</tt> <font size="1">(NOT SUPPORTED)</font></td></tr> <tr><td><font color="blue"><tt>(?&lt;!re)</tt></font></td><td>after text not matching <tt>re</tt> <font size="1">(NOT SUPPORTED)</font></td></tr> <tr><td><font color="blue"><tt>re&amp;</tt></font></td><td>before text matching <tt>re</tt> <font size="1">(NOT SUPPORTED)</font> <font size="1">VIM</font></td></tr> <tr><td><font color="blue"><tt>re@=</tt></font></td><td>before text matching <tt>re</tt> <font size="1">(NOT SUPPORTED)</font> <font size="1">VIM</font></td></tr> <tr><td><font color="blue"><tt>re@!</tt></font></td><td>before text not matching <tt>re</tt> <font size="1">(NOT SUPPORTED)</font> <font size="1">VIM</font></td></tr> <tr><td><font color="blue"><tt>re@&lt;=</tt></font></td><td>after text matching <tt>re</tt> <font size="1">(NOT SUPPORTED)</font> <font size="1">VIM</font></td></tr> <tr><td><font color="blue"><tt>re@&lt;!</tt></font></td><td>after text not matching <tt>re</tt> <font size="1">(NOT SUPPORTED)</font> <font size="1">VIM</font></td></tr> <tr><td><font color="blue"><tt>\zs</tt></font></td><td>sets start of match (= \K) <font size="1">(NOT SUPPORTED)</font> <font size="1">VIM</font></td></tr> <tr><td><font color="blue"><tt>\ze</tt></font></td><td>sets end of match <font size="1">(NOT SUPPORTED)</font> <font size="1">VIM</font></td></tr> <tr><td><font color="blue"><tt>\%^</tt></font></td><td>beginning of file <font size="1">(NOT SUPPORTED)</font> <font size="1">VIM</font></td></tr> <tr><td><font color="blue"><tt>\%$</tt></font></td><td>end of file <font size="1">(NOT SUPPORTED)</font> <font size="1">VIM</font></td></tr> <tr><td><font color="blue"><tt>\%V</tt></font></td><td>on screen <font size="1">(NOT SUPPORTED)</font> <font size="1">VIM</font></td></tr> <tr><td><font color="blue"><tt>\%#</tt></font></td><td>cursor position <font size="1">(NOT SUPPORTED)</font> <font size="1">VIM</font></td></tr> <tr><td><font color="blue"><tt>\%&#x27;m</tt></font></td><td>mark <tt>m</tt> position <font size="1">(NOT SUPPORTED)</font> <font size="1">VIM</font></td></tr> <tr><td><font color="blue"><tt>\%23l</tt></font></td><td>in line 23 <font size="1">(NOT SUPPORTED)</font> <font size="1">VIM</font></td></tr> <tr><td><font color="blue"><tt>\%23c</tt></font></td><td>in column 23 <font size="1">(NOT SUPPORTED)</font> <font size="1">VIM</font></td></tr> <tr><td><font color="blue"><tt>\%23v</tt></font></td><td>in virtual column 23 <font size="1">(NOT SUPPORTED)</font> <font size="1">VIM</font></td></tr> <tr><td></td></tr> <tr><td colspan="2"><b>Escape sequences:</b></td></tr> <tr><td><tt>\a</tt></td><td>bell (≡ <tt>\007</tt>)</td></tr> <tr><td><tt>\f</tt></td><td>form feed (≡ <tt>\014</tt>)</td></tr> <tr><td><tt>\t</tt></td><td>horizontal tab (≡ <tt>\011</tt>)</td></tr> <tr><td><tt>\n</tt></td><td>newline (≡ <tt>\012</tt>)</td></tr> <tr><td><tt>\r</tt></td><td>carriage return (≡ <tt>\015</tt>)</td></tr> <tr><td><tt>\v</tt></td><td>vertical tab character (≡ <tt>\013</tt>)</td></tr> <tr><td><tt>\*</tt></td><td>literal <tt>*</tt>, for any punctuation character <tt>*</tt></td></tr> <tr><td><tt>\123</tt></td><td>octal character code (up to three digits)</td></tr> <tr><td><tt>\x7F</tt></td><td>hex character code (exactly two digits)</td></tr> <tr><td><tt>\x{10FFFF}</tt></td><td>hex character code</td></tr> <tr><td><tt>\C</tt></td><td>match a single byte even in UTF-8 mode</td></tr> <tr><td><tt>\Q...\E</tt></td><td>literal text <tt>...</tt> even if <tt>...</tt> has punctuation</td></tr> <tr><td></td></tr> <tr><td><font color="blue"><tt>\1</tt></font></td><td>backreference <font size="1">(NOT SUPPORTED)</font></td></tr> <tr><td><font color="blue"><tt>\b</tt></font></td><td>backspace <font size="1">(NOT SUPPORTED)</font> (use <tt>\010</tt>)</td></tr> <tr><td><font color="blue"><tt>\cK</tt></font></td><td>control char ^K <font size="1">(NOT SUPPORTED)</font> (use <tt>\001</tt> etc)</td></tr> <tr><td><font color="blue"><tt>\e</tt></font></td><td>escape <font size="1">(NOT SUPPORTED)</font> (use <tt>\033</tt>)</td></tr> <tr><td><font color="blue"><tt>\g1</tt></font></td><td>backreference <font size="1">(NOT SUPPORTED)</font></td></tr> <tr><td><font color="blue"><tt>\g{1}</tt></font></td><td>backreference <font size="1">(NOT SUPPORTED)</font></td></tr> <tr><td><font color="blue"><tt>\g{+1}</tt></font></td><td>backreference <font size="1">(NOT SUPPORTED)</font></td></tr> <tr><td><font color="blue"><tt>\g{-1}</tt></font></td><td>backreference <font size="1">(NOT SUPPORTED)</font></td></tr> <tr><td><font color="blue"><tt>\g{name}</tt></font></td><td>named backreference <font size="1">(NOT SUPPORTED)</font></td></tr> <tr><td><font color="blue"><tt>\g&lt;name&gt;</tt></font></td><td>subroutine call <font size="1">(NOT SUPPORTED)</font></td></tr> <tr><td><font color="blue"><tt>\g&#x27;name&#x27;</tt></font></td><td>subroutine call <font size="1">(NOT SUPPORTED)</font></td></tr> <tr><td><font color="blue"><tt>\k&lt;name&gt;</tt></font></td><td>named backreference <font size="1">(NOT SUPPORTED)</font></td></tr> <tr><td><font color="blue"><tt>\k&#x27;name&#x27;</tt></font></td><td>named backreference <font size="1">(NOT SUPPORTED)</font></td></tr> <tr><td><font color="blue"><tt>\lX</tt></font></td><td>lowercase <tt>X</tt> <font size="1">(NOT SUPPORTED)</font></td></tr> <tr><td><font color="blue"><tt>\ux</tt></font></td><td>uppercase <tt>x</tt> <font size="1">(NOT SUPPORTED)</font></td></tr> <tr><td><font color="blue"><tt>\L...\E</tt></font></td><td>lowercase text <tt>...</tt> <font size="1">(NOT SUPPORTED)</font></td></tr> <tr><td><font color="blue"><tt>\K</tt></font></td><td>reset beginning of <tt>$0</tt> <font size="1">(NOT SUPPORTED)</font></td></tr> <tr><td><font color="blue"><tt>\N{name}</tt></font></td><td>named Unicode character <font size="1">(NOT SUPPORTED)</font></td></tr> <tr><td><font color="blue"><tt>\R</tt></font></td><td>line break <font size="1">(NOT SUPPORTED)</font></td></tr> <tr><td><font color="blue"><tt>\U...\E</tt></font></td><td>upper case text <tt>...</tt> <font size="1">(NOT SUPPORTED)</font></td></tr> <tr><td><font color="blue"><tt>\X</tt></font></td><td>extended Unicode sequence <font size="1">(NOT SUPPORTED)</font></td></tr> <tr><td></td></tr> <tr><td><font color="blue"><tt>\%d123</tt></font></td><td>decimal character 123 <font size="1">(NOT SUPPORTED)</font> <font size="1">VIM</font></td></tr> <tr><td><font color="blue"><tt>\%xFF</tt></font></td><td>hex character FF <font size="1">(NOT SUPPORTED)</font> <font size="1">VIM</font></td></tr> <tr><td><font color="blue"><tt>\%o123</tt></font></td><td>octal character 123 <font size="1">(NOT SUPPORTED)</font> <font size="1">VIM</font></td></tr> <tr><td><font color="blue"><tt>\%u1234</tt></font></td><td>Unicode character 0x1234 <font size="1">(NOT SUPPORTED)</font> <font size="1">VIM</font></td></tr> <tr><td><font color="blue"><tt>\%U12345678</tt></font></td><td>Unicode character 0x12345678 <font size="1">(NOT SUPPORTED)</font> <font size="1">VIM</font></td></tr> <tr><td></td></tr> <tr><td colspan="2"><b>Character class elements:</b></td></tr> <tr><td><tt>x</tt></td><td>single character</td></tr> <tr><td><tt>A-Z</tt></td><td>character range (inclusive)</td></tr> <tr><td><tt>\d</tt></td><td>Perl character class</td></tr> <tr><td><tt>[:foo:]</tt></td><td>ASCII character class <tt>foo</tt></td></tr> <tr><td><tt>\p{Foo}</tt></td><td>Unicode character class <tt>Foo</tt></td></tr> <tr><td><tt>\pF</tt></td><td>Unicode character class <tt>F</tt> (one-letter name)</td></tr> <tr><td></td></tr> <tr><td colspan="2"><b>Named character classes as character class elements:</b></td></tr> <tr><td><tt>[\d]</tt></td><td>digits (≡ <tt>\d</tt>)</td></tr> <tr><td><tt>[^\d]</tt></td><td>not digits (≡ <tt>\D</tt>)</td></tr> <tr><td><tt>[\D]</tt></td><td>not digits (≡ <tt>\D</tt>)</td></tr> <tr><td><tt>[^\D]</tt></td><td>not not digits (≡ <tt>\d</tt>)</td></tr> <tr><td><tt>[[:name:]]</tt></td><td>named ASCII class inside character class (≡ <tt>[:name:]</tt>)</td></tr> <tr><td><tt>[^[:name:]]</tt></td><td>named ASCII class inside negated character class (≡ <tt>[:^name:]</tt>)</td></tr> <tr><td><tt>[\p{Name}]</tt></td><td>named Unicode property inside character class (≡ <tt>\p{Name}</tt>)</td></tr> <tr><td><tt>[^\p{Name}]</tt></td><td>named Unicode property inside negated character class (≡ <tt>\P{Name}</tt>)</td></tr> <tr><td></td></tr> <tr><td colspan="2"><b>Perl character classes:</b></td></tr> <tr><td><tt>\d</tt></td><td>digits (≡ <tt>[0-9]</tt>)</td></tr> <tr><td><tt>\D</tt></td><td>not digits (≡ <tt>[^0-9]</tt>)</td></tr> <tr><td><tt>\s</tt></td><td>whitespace (≡ <tt>[\t\n\f\r ]</tt>)</td></tr> <tr><td><tt>\S</tt></td><td>not whitespace (≡ <tt>[^\t\n\f\r ]</tt>)</td></tr> <tr><td><tt>\w</tt></td><td>word characters (≡ <tt>[0-9A-Za-z_]</tt>)</td></tr> <tr><td><tt>\W</tt></td><td>not word characters (≡ <tt>[^0-9A-Za-z_]</tt>)</td></tr> <tr><td></td></tr> <tr><td><font color="blue"><tt>\h</tt></font></td><td>horizontal space <font size="1">(NOT SUPPORTED)</font></td></tr> <tr><td><font color="blue"><tt>\H</tt></font></td><td>not horizontal space <font size="1">(NOT SUPPORTED)</font></td></tr> <tr><td><font color="blue"><tt>\v</tt></font></td><td>vertical space <font size="1">(NOT SUPPORTED)</font></td></tr> <tr><td><font color="blue"><tt>\V</tt></font></td><td>not vertical space <font size="1">(NOT SUPPORTED)</font></td></tr> <tr><td></td></tr> <tr><td colspan="2"><b>ASCII character classes:</b></td></tr> <tr><td><tt>[:alnum:]</tt></td><td>alphanumeric (≡ <tt>[0-9A-Za-z]</tt>)</td></tr> <tr><td><tt>[:alpha:]</tt></td><td>alphabetic (≡ <tt>[A-Za-z]</tt>)</td></tr> <tr><td><tt>[:ascii:]</tt></td><td>ASCII (≡ <tt>[\x00-\x7F]</tt>)</td></tr> <tr><td><tt>[:blank:]</tt></td><td>blank (≡ <tt>[\t ]</tt>)</td></tr> <tr><td><tt>[:cntrl:]</tt></td><td>control (≡ <tt>[\x00-\x1F\x7F]</tt>)</td></tr> <tr><td><tt>[:digit:]</tt></td><td>digits (≡ <tt>[0-9]</tt>)</td></tr> <tr><td><tt>[:graph:]</tt></td><td>graphical (≡ <tt>[!-~] == [A-Za-z0-9!&quot;#$%&amp;&#x27;()*+,\-./:;&lt;=&gt;?@[\\\]^_</tt><tt>`</tt><tt>{|}~]</tt>)</td></tr> <tr><td><tt>[:lower:]</tt></td><td>lower case (≡ <tt>[a-z]</tt>)</td></tr> <tr><td><tt>[:print:]</tt></td><td>printable (≡ <tt>[ -~] == [ [:graph:]]</tt>)</td></tr> <tr><td><tt>[:punct:]</tt></td><td>punctuation (≡ <tt>[!-/:-@[-</tt><tt>`</tt><tt>{-~]</tt>)</td></tr> <tr><td><tt>[:space:]</tt></td><td>whitespace (≡ <tt>[\t\n\v\f\r ]</tt>)</td></tr> <tr><td><tt>[:upper:]</tt></td><td>upper case (≡ <tt>[A-Z]</tt>)</td></tr> <tr><td><tt>[:word:]</tt></td><td>word characters (≡ <tt>[0-9A-Za-z_]</tt>)</td></tr> <tr><td><tt>[:xdigit:]</tt></td><td>hex digit (≡ <tt>[0-9A-Fa-f]</tt>)</td></tr> <tr><td></td></tr> <tr><td colspan="2"><b>Unicode character class names--general category:</b></td></tr> <tr><td><tt>C</tt></td><td>other</td></tr> <tr><td><tt>Cc</tt></td><td>control</td></tr> <tr><td><tt>Cf</tt></td><td>format</td></tr> <tr><td><font color="blue"><tt>Cn</tt></font></td><td>unassigned code points <font size="1">(NOT SUPPORTED)</font></td></tr> <tr><td><tt>Co</tt></td><td>private use</td></tr> <tr><td><tt>Cs</tt></td><td>surrogate</td></tr> <tr><td><tt>L</tt></td><td>letter</td></tr> <tr><td><font color="blue"><tt>LC</tt></font></td><td>cased letter <font size="1">(NOT SUPPORTED)</font></td></tr> <tr><td><font color="blue"><tt>L&amp;</tt></font></td><td>cased letter <font size="1">(NOT SUPPORTED)</font></td></tr> <tr><td><tt>Ll</tt></td><td>lowercase letter</td></tr> <tr><td><tt>Lm</tt></td><td>modifier letter</td></tr> <tr><td><tt>Lo</tt></td><td>other letter</td></tr> <tr><td><tt>Lt</tt></td><td>titlecase letter</td></tr> <tr><td><tt>Lu</tt></td><td>uppercase letter</td></tr> <tr><td><tt>M</tt></td><td>mark</td></tr> <tr><td><tt>Mc</tt></td><td>spacing mark</td></tr> <tr><td><tt>Me</tt></td><td>enclosing mark</td></tr> <tr><td><tt>Mn</tt></td><td>non-spacing mark</td></tr> <tr><td><tt>N</tt></td><td>number</td></tr> <tr><td><tt>Nd</tt></td><td>decimal number</td></tr> <tr><td><tt>Nl</tt></td><td>letter number</td></tr> <tr><td><tt>No</tt></td><td>other number</td></tr> <tr><td><tt>P</tt></td><td>punctuation</td></tr> <tr><td><tt>Pc</tt></td><td>connector punctuation</td></tr> <tr><td><tt>Pd</tt></td><td>dash punctuation</td></tr> <tr><td><tt>Pe</tt></td><td>close punctuation</td></tr> <tr><td><tt>Pf</tt></td><td>final punctuation</td></tr> <tr><td><tt>Pi</tt></td><td>initial punctuation</td></tr> <tr><td><tt>Po</tt></td><td>other punctuation</td></tr> <tr><td><tt>Ps</tt></td><td>open punctuation</td></tr> <tr><td><tt>S</tt></td><td>symbol</td></tr> <tr><td><tt>Sc</tt></td><td>currency symbol</td></tr> <tr><td><tt>Sk</tt></td><td>modifier symbol</td></tr> <tr><td><tt>Sm</tt></td><td>math symbol</td></tr> <tr><td><tt>So</tt></td><td>other symbol</td></tr> <tr><td><tt>Z</tt></td><td>separator</td></tr> <tr><td><tt>Zl</tt></td><td>line separator</td></tr> <tr><td><tt>Zp</tt></td><td>paragraph separator</td></tr> <tr><td><tt>Zs</tt></td><td>space separator</td></tr> <tr><td></td></tr> <tr><td colspan="2"><b>Unicode character class names--scripts:</b></td></tr> <tr><td><tt>Arabic</tt></td><td>Arabic</td></tr> <tr><td><tt>Armenian</tt></td><td>Armenian</td></tr> <tr><td><tt>Balinese</tt></td><td>Balinese</td></tr> <tr><td><tt>Bengali</tt></td><td>Bengali</td></tr> <tr><td><tt>Bopomofo</tt></td><td>Bopomofo</td></tr> <tr><td><tt>Braille</tt></td><td>Braille</td></tr> <tr><td><tt>Buginese</tt></td><td>Buginese</td></tr> <tr><td><tt>Buhid</tt></td><td>Buhid</td></tr> <tr><td><tt>Canadian_Aboriginal</tt></td><td>Canadian Aboriginal</td></tr> <tr><td><tt>Carian</tt></td><td>Carian</td></tr> <tr><td><tt>Cham</tt></td><td>Cham</td></tr> <tr><td><tt>Cherokee</tt></td><td>Cherokee</td></tr> <tr><td><tt>Common</tt></td><td>characters not specific to one script</td></tr> <tr><td><tt>Coptic</tt></td><td>Coptic</td></tr> <tr><td><tt>Cuneiform</tt></td><td>Cuneiform</td></tr> <tr><td><tt>Cypriot</tt></td><td>Cypriot</td></tr> <tr><td><tt>Cyrillic</tt></td><td>Cyrillic</td></tr> <tr><td><tt>Deseret</tt></td><td>Deseret</td></tr> <tr><td><tt>Devanagari</tt></td><td>Devanagari</td></tr> <tr><td><tt>Ethiopic</tt></td><td>Ethiopic</td></tr> <tr><td><tt>Georgian</tt></td><td>Georgian</td></tr> <tr><td><tt>Glagolitic</tt></td><td>Glagolitic</td></tr> <tr><td><tt>Gothic</tt></td><td>Gothic</td></tr> <tr><td><tt>Greek</tt></td><td>Greek</td></tr> <tr><td><tt>Gujarati</tt></td><td>Gujarati</td></tr> <tr><td><tt>Gurmukhi</tt></td><td>Gurmukhi</td></tr> <tr><td><tt>Han</tt></td><td>Han</td></tr> <tr><td><tt>Hangul</tt></td><td>Hangul</td></tr> <tr><td><tt>Hanunoo</tt></td><td>Hanunoo</td></tr> <tr><td><tt>Hebrew</tt></td><td>Hebrew</td></tr> <tr><td><tt>Hiragana</tt></td><td>Hiragana</td></tr> <tr><td><tt>Inherited</tt></td><td>inherit script from previous character</td></tr> <tr><td><tt>Kannada</tt></td><td>Kannada</td></tr> <tr><td><tt>Katakana</tt></td><td>Katakana</td></tr> <tr><td><tt>Kayah_Li</tt></td><td>Kayah Li</td></tr> <tr><td><tt>Kharoshthi</tt></td><td>Kharoshthi</td></tr> <tr><td><tt>Khmer</tt></td><td>Khmer</td></tr> <tr><td><tt>Lao</tt></td><td>Lao</td></tr> <tr><td><tt>Latin</tt></td><td>Latin</td></tr> <tr><td><tt>Lepcha</tt></td><td>Lepcha</td></tr> <tr><td><tt>Limbu</tt></td><td>Limbu</td></tr> <tr><td><tt>Linear_B</tt></td><td>Linear B</td></tr> <tr><td><tt>Lycian</tt></td><td>Lycian</td></tr> <tr><td><tt>Lydian</tt></td><td>Lydian</td></tr> <tr><td><tt>Malayalam</tt></td><td>Malayalam</td></tr> <tr><td><tt>Mongolian</tt></td><td>Mongolian</td></tr> <tr><td><tt>Myanmar</tt></td><td>Myanmar</td></tr> <tr><td><tt>New_Tai_Lue</tt></td><td>New Tai Lue (aka Simplified Tai Lue)</td></tr> <tr><td><tt>Nko</tt></td><td>Nko</td></tr> <tr><td><tt>Ogham</tt></td><td>Ogham</td></tr> <tr><td><tt>Ol_Chiki</tt></td><td>Ol Chiki</td></tr> <tr><td><tt>Old_Italic</tt></td><td>Old Italic</td></tr> <tr><td><tt>Old_Persian</tt></td><td>Old Persian</td></tr> <tr><td><tt>Oriya</tt></td><td>Oriya</td></tr> <tr><td><tt>Osmanya</tt></td><td>Osmanya</td></tr> <tr><td><tt>Phags_Pa</tt></td><td>&#x27;Phags Pa</td></tr> <tr><td><tt>Phoenician</tt></td><td>Phoenician</td></tr> <tr><td><tt>Rejang</tt></td><td>Rejang</td></tr> <tr><td><tt>Runic</tt></td><td>Runic</td></tr> <tr><td><tt>Saurashtra</tt></td><td>Saurashtra</td></tr> <tr><td><tt>Shavian</tt></td><td>Shavian</td></tr> <tr><td><tt>Sinhala</tt></td><td>Sinhala</td></tr> <tr><td><tt>Sundanese</tt></td><td>Sundanese</td></tr> <tr><td><tt>Syloti_Nagri</tt></td><td>Syloti Nagri</td></tr> <tr><td><tt>Syriac</tt></td><td>Syriac</td></tr> <tr><td><tt>Tagalog</tt></td><td>Tagalog</td></tr> <tr><td><tt>Tagbanwa</tt></td><td>Tagbanwa</td></tr> <tr><td><tt>Tai_Le</tt></td><td>Tai Le</td></tr> <tr><td><tt>Tamil</tt></td><td>Tamil</td></tr> <tr><td><tt>Telugu</tt></td><td>Telugu</td></tr> <tr><td><tt>Thaana</tt></td><td>Thaana</td></tr> <tr><td><tt>Thai</tt></td><td>Thai</td></tr> <tr><td><tt>Tibetan</tt></td><td>Tibetan</td></tr> <tr><td><tt>Tifinagh</tt></td><td>Tifinagh</td></tr> <tr><td><tt>Ugaritic</tt></td><td>Ugaritic</td></tr> <tr><td><tt>Vai</tt></td><td>Vai</td></tr> <tr><td><tt>Yi</tt></td><td>Yi</td></tr> <tr><td></td></tr> <tr><td colspan="2"><b>Vim character classes:</b></td></tr> <tr><td><font color="blue"><tt>\i</tt></font></td><td>identifier character <font size="1">(NOT SUPPORTED)/font> <font size="1">VIM</font></td></tr> <tr><td><font color="blue"><tt>\I</tt></font></td><td><tt>\i</tt> except digits <font size="1">(NOT SUPPORTED)</font> <font size="1">VIM</font></td></tr> <tr><td><font color="blue"><tt>\k</tt></font></td><td>keyword character <font size="1">(NOT SUPPORTED)</font> <font size="1">VIM</font></td></tr> <tr><td><font color="blue"><tt>\K</tt></font></td><td><tt>\k</tt> except digits <font size="1">(NOT SUPPORTED)</font> <font size="1">VIM</font></td></tr> <tr><td><font color="blue"><tt>\f</tt></font></td><td>file name character <font size="1">(NOT SUPPORTED)</font> <font size="1">VIM</font></td></tr> <tr><td><font color="blue"><tt>\F</tt></font></td><td><tt>\f</tt> except digits <font size="1">(NOT SUPPORTED)</font> <font size="1">VIM</font></td></tr> <tr><td><font color="blue"><tt>\p</tt></font></td><td>printable character <font size="1">(NOT SUPPORTED)</font> <font size="1">VIM</font></td></tr> <tr><td><font color="blue"><tt>\P</tt></font></td><td><tt>\p</tt> except digits <font size="1">(NOT SUPPORTED)</font> <font size="1">VIM</font></td></tr> <tr><td><font color="blue"><tt>\s</tt></font></td><td>whitespace character (≡ <tt>[ \t]</tt>) <font size="1">(NOT SUPPORTED)</font> <font size="1">VIM</font></td></tr> <tr><td><font color="blue"><tt>\S</tt></font></td><td>non-white space character (≡ <tt>[^ \t]</tt>) <font size="1">(NOT SUPPORTED)</font> <font size="1">VIM</font></td></tr> <tr><td><tt>\d</tt></td><td>digits (≡ <tt>[0-9]</tt>) <font size="1">VIM</font></td></tr> <tr><td><tt>\D</tt></td><td>not <tt>\d</tt> <font size="1">VIM</font></td></tr> <tr><td><font color="blue"><tt>\x</tt></font></td><td>hex digits (≡ <tt>[0-9A-Fa-f]</tt>) <font size="1">(NOT SUPPORTED)</font> <font size="1">VIM</font></td></tr> <tr><td><font color="blue"><tt>\X</tt></font></td><td>not <tt>\x</tt> <font size="1">(NOT SUPPORTED)</font> <font size="1">VIM</font></td></tr> <tr><td><font color="blue"><tt>\o</tt></font></td><td>octal digits (≡ <tt>[0-7]</tt>) <font size="1">(NOT SUPPORTED)</font> <font size="1">VIM</font></td></tr> <tr><td><font color="blue"><tt>\O</tt></font></td><td>not <tt>\o</tt> <font size="1">(NOT SUPPORTED)</font> <font size="1">VIM</font></td></tr> <tr><td><tt>\w</tt></td><td>word character <font size="1">VIM</font></td></tr> <tr><td><tt>\W</tt></td><td>not <tt>\w</tt> <font size="1">VIM</font></td></tr> <tr><td><font color="blue"><tt>\h</tt></font></td><td>head of word character <font size="1">(NOT SUPPORTED)</font> <font size="1">VIM</font></td></tr> <tr><td><font color="blue"><tt>\H</tt></font></td><td>not <tt>\h</tt> <font size="1">(NOT SUPPORTED)</font> <font size="1">VIM</font></td></tr> <tr><td><font color="blue"><tt>\a</tt></font></td><td>alphabetic <font size="1">(NOT SUPPORTED)</font> <font size="1">VIM</font></td></tr> <tr><td><font color="blue"><tt>\A</tt></font></td><td>not <tt>\a</tt> <font size="1">(NOT SUPPORTED)</font> <font size="1">VIM</font></td></tr> <tr><td><font color="blue"><tt>\l</tt></font></td><td>lowercase <font size="1">(NOT SUPPORTED)</font> <font size="1">VIM</font></td></tr> <tr><td><font color="blue"><tt>\L</tt></font></td><td>not lowercase <font size="1">(NOT SUPPORTED)</font> <font size="1">VIM</font></td></tr> <tr><td><font color="blue"><tt>\u</tt></font></td><td>uppercase <font size="1">(NOT SUPPORTED)</font> <font size="1">VIM</font></td></tr> <tr><td><font color="blue"><tt>\U</tt></font></td><td>not uppercase <font size="1">(NOT SUPPORTED)</font> <font size="1">VIM</font></td></tr> <tr><td><font color="blue"><tt>\_x</tt></font></td><td><tt>\x</tt> plus newline, for any <tt>x</tt> <font size="1">(NOT SUPPORTED)</font> <font size="1">VIM</font></td></tr> <tr><td></td></tr> <tr><td colspan="2"><b>Vim flags:</b></td></tr> <tr><td><font color="blue"><tt>\c</tt></font></td><td>ignore case <font size="1">(NOT SUPPORTED)</font> <font size="1">VIM</font></td></tr> <tr><td><font color="blue"><tt>\C</tt></font></td><td>match case <font size="1">(NOT SUPPORTED)</font> <font size="1">VIM</font></td></tr> <tr><td><font color="blue"><tt>\m</tt></font></td><td>magic <font size="1">(NOT SUPPORTED)</font> <font size="1">VIM</font></td></tr> <tr><td><font color="blue"><tt>\M</tt></font></td><td>nomagic <font size="1">(NOT SUPPORTED)</font> <font size="1">VIM</font></td></tr> <tr><td><font color="blue"><tt>\v</tt></font></td><td>verymagic <font size="1">(NOT SUPPORTED)</font> <font size="1">VIM</font></td></tr> <tr><td><font color="blue"><tt>\V</tt></font></td><td>verynomagic <font size="1">(NOT SUPPORTED)</font> <font size="1">VIM</font></td></tr> <tr><td><font color="blue"><tt>\Z</tt></font></td><td>ignore differences in Unicode combining characters <font size="1">(NOT SUPPORTED)</font> <font size="1">VIM</font></td></tr> <tr><td></td></tr> <tr><td colspan="2"><b>Magic:</b></td></tr> <tr><td><font color="blue"><tt>(?{code})</tt></font></td><td>arbitrary Perl code <font size="1">(NOT SUPPORTED)</font> <font size="1">PERL</font></td></tr> <tr><td><font color="blue"><tt>(??{code})</tt></font></td><td>postponed arbitrary Perl code <font size="1">(NOT SUPPORTED)</font> <font size="1">PERL</font></td></tr> <tr><td><font color="blue"><tt>(?n)</tt></font></td><td>recursive call to regexp capturing group <tt>n</tt> <font size="1">(NOT SUPPORTED)</font></td></tr> <tr><td><font color="blue"><tt>(?+n)</tt></font></td><td>recursive call to relative group <tt>+n</tt> <font size="1">(NOT SUPPORTED)</font></td></tr> <tr><td><font color="blue"><tt>(?-n)</tt></font></td><td>recursive call to relative group <tt>-n</tt> <font size="1">(NOT SUPPORTED)</font></td></tr> <tr><td><font color="blue"><tt>(?C)</tt></font></td><td>PCRE callout <font size="1">(NOT SUPPORTED)</font> <font size="1">PCRE</font></td></tr> <tr><td><font color="blue"><tt>(?R)</tt></font></td><td>recursive call to entire regexp (≡ <tt>(?0)</tt>) <font size="1">(NOT SUPPORTED)</font></td></tr> <tr><td><font color="blue"><tt>(?&amp;name)</tt></font></td><td>recursive call to named group <font size="1">(NOT SUPPORTED)</font></td></tr> <tr><td><font color="blue"><tt>(?P=name)</tt></font></td><td>named backreference <font size="1">(NOT SUPPORTED)</font></td></tr> <tr><td><font color="blue"><tt>(?P&gt;name)</tt></font></td><td>recursive call to named group <font size="1">(NOT SUPPORTED)</font></td></tr> <tr><td><font color="blue"><tt>(?(cond)true|false)</tt></font></td><td>conditional branch <font size="1">(NOT SUPPORTED)</font></td></tr> <tr><td><font color="blue"><tt>(?(cond)true)</tt></font></td><td>conditional branch <font size="1">(NOT SUPPORTED)</font></td></tr> <tr><td><font color="blue"><tt>(*ACCEPT)</tt></font></td><td>make regexps more like Prolog <font size="1">(NOT SUPPORTED)</font></td></tr> <tr><td><font color="blue"><tt>(*COMMIT)</tt></font></td><td><font size="1">(NOT SUPPORTED)</font></td></tr> <tr><td><font color="blue"><tt>(*F)</tt></font></td><td><font size="1">(NOT SUPPORTED)</font></td></tr> <tr><td><font color="blue"><tt>(*FAIL)</tt></font></td><td><font size="1">(NOT SUPPORTED)</font></td></tr> <tr><td><font color="blue"><tt>(*MARK)</tt></font></td><td><font size="1">(NOT SUPPORTED)</font></td></tr> <tr><td><font color="blue"><tt>(*PRUNE)</tt></font></td><td><font size="1">(NOT SUPPORTED)</font></td></tr> <tr><td><font color="blue"><tt>(*SKIP)</tt></font></td><td><font size="1">(NOT SUPPORTED)</font></td></tr> <tr><td><font color="blue"><tt>(*THEN)</tt></font></td><td><font size="1">(NOT SUPPORTED)</font></td></tr> <tr><td><font color="blue"><tt>(*ANY)</tt></font></td><td>set newline convention <font size="1">(NOT SUPPORTED)</font></td></tr> <tr><td><font color="blue"><tt>(*ANYCRLF)</tt></font></td><td><font size="1">(NOT SUPPORTED)</font></td></tr> <tr><td><font color="blue"><tt>(*CR)</tt></font></td><td><font size="1">(NOT SUPPORTED)</font></td></tr> <tr><td><font color="blue"><tt>(*CRLF)</tt></font></td><td><font size="1">(NOT SUPPORTED)</font></td></tr> <tr><td><font color="blue"><tt>(*LF)</tt></font></td><td><font size="1">(NOT SUPPORTED)</font></td></tr> <tr><td><font color="blue"><tt>(*BSR_ANYCRLF)</tt></font></td><td>set \R convention <font size="1">(NOT SUPPORTED)</font> <font size="1">PCRE</font></td></tr> <tr><td><font color="blue"><tt>(*BSR_UNICODE)</tt></font></td><td><font size="1">(NOT SUPPORTED)</font> <font size="1">PCRE</font></td></tr> <tr><td></td></tr> </table> </p>


## 扩展阅读

1. "perlre - Perl regular expressions" [http://perldoc.perl.org/perlre.html](http://perldoc.perl.org/perlre.html)

2. "Implementing Regular Expressions" [http://swtch.com/~rsc/regexp](http://swtch.com/~rsc/regexp)

3. The re1 project: [http://code.google.com/p/re1](http://code.google.com/p/re1)

4. The re2 project: [http://code.google.com/p/re2](http://code.google.com/p/re2)

5. sregex: [A non-backtracking regex engine matching on data streams](https://github.com/agentzh/sregex)

6. sregex: matching Perl 5 regexes on data streams: [http://agentzh.org/misc/slides/yapc-na-2013-sregex.pdf](http://agentzh.org/misc/slides/yapc-na-2013-sregex.pdf)


## 参考资料

* `RE2`官网资料：[http://code.google.com/p/re2/wiki/](http://code.google.com/p/re2/wiki/)

* `sregex`: matching Perl 5 regexes on data streams: [http://agentzh.org/misc/slides/yapc-na-2013-sregex.pdf](http://agentzh.org/misc/slides/yapc-na-2013-sregex.pdf)


## 祝大家玩的开心

