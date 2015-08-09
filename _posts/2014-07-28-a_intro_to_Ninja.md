---
layout: post
title: Ninja - chromium核心构建工具
date: 2014-07-28
categories:
  - 技术
tags:
  - Ninja
---
## 缘由

经过上次对[chromium](http://code.google.com/p/chromium)核心代码的初步了解之后，我转头去研究了一番[ninja](https://github.com/martine/ninja)，并对其[进行了一些改造](https://github.com/guiquanz/ninja)（爱折腾的，都是小NB）。今天就来简单介绍一下ninja及其使用。(BTW: 细节的内容，大家阅读[ninja 的手册](http://martine.github.io/ninja/manual.html)就好了，我这里不会关注。)

[ninja](https://github.com/martine/ninja)是__一个专注于速度的__小型`构建系统`（Ninja is a small build system with a focus on speed）。ninja是其作者为了解决`chromium代码编译慢`这个问题（具体一点，就是发生在将Chrome移植到非Windows平台过程中的事情。欲知详情，请阅读[Ninja, a new build system](http://neugierig.org/software/chromium/notes/2011/02/ninja.html)）而诞生的。其设计受到[the tup build system](http://gittup.org/tup/)和[redo](https://github.com/apenwarr/redo)的启发。ninja核心是由C/C++编写的，同时有一部分辅助功能由`python`和`shell`实现。

ninja可以很好的组合[gyp](https://code.google.com/p/gyp)和[CMake](http://www.cmake.org/)一起使用，后者为其生成.ninja文件。

ninja项目的最终编译产出物是一个可执行文件ninja。


## 下载代码 并 编译

```bash

mkdir -p ~/ninja && cd ~/ninja
git clone https://github.com/martine/ninja
cd ninja
python ./bootstrap.py
```
（BTW：以上过程编译生成可执行文件ninja。需要预先安装 [graphviz](https://github.com/ellson/graphviz)及其开发库，[gtest](https://code.google.com/p/googletest)，[git](http://www.git-scm.com)、[re2c](http://re2c.org/)和[python](http://www.python.org)）


## 测试

由于在编译ninja的过程中`bootstrap.py`脚本通过调用`configure.py`和`platform_helper.py`生成了ninja项目的构建文件`build.ninja`，所以我们只需要执行`./ninja ninja_test`就可以通过ninja构建生成测试文件`ninja_test`。这样就可以执行测试了。

```bash

./ninja ninja_test
./ninja all

```


## ninja 工具介绍

在介绍ninja的文法之前，还是先了解一下ninja的使用吧。执行`./ninja -h`显示帮助信息。具体参数说明，如下：

```bash

usage: ninja [options] [targets...]

if targets are unspecified, builds the 'default' target (see manual).

options:
  --version  # 打印版本信息（如当前版本是1.5.1）

  -C DIR   # 在执行操作之前，切换到`DIR`目录
  -f FILE  # 制定`FILE`为构建输入文件。默认文件为当前目录下的`build.ninja`。如 ./ninja -f demo.ninja

  -j N     # 并行执行 N 个作业。默认N=3（需要对应的CPU支持）。如 ./ninja -j 2 all
  -l N     # 如果平均负载大于N，不启动新的作业
  -k N     # 持续构建直到N个作业失败为止。默认N=1
  -n       # 排练（dry run）(不执行命令，视其成功执行。如 ./ninja -n -t clean)
  -v       # 显示构建中的所有命令行（这个对实际构建的命令核对非常有用）

  -d MODE  # 开启调试模式 (用 -d list 罗列所有的模式)
  -t TOOL  # 执行一个子工具(用 -t list 罗列所有子命令工具)。如 ./ninja -t query all
```

ninja还集成了graphviz等一些对开发非常有用的工具。具体如下：（也就是执行 `./ninja -t list` 的结果）

```bash

ninja subtools:
    browse  # 在浏览器中浏览依赖关系图。（默认会在8080端口启动一个基于python的http服务）
     clean  # 清除构建生成的文件
  commands  # 罗列重新构建制定目标所需的所有命令
      deps  # 显示存储在deps日志中的依赖关系
     graph  # 为指定目标生成 graphviz dot 文件。如 ninja -t graph all |dot -Tpng -o graph.png
     query  # 显示一个路径的inputs/outputs
   targets  # 通过DAG中rule或depth罗列target
    compdb  # dump JSON兼容的数据库到标准输出
 recompact  # 重新紧凑化ninja内部数据结构
```


## ninja文件示例

聊了半天，ninja的构建文件长什么模样呢？以下的demo就是一个执行`echo`，打印一行文字的ninja构建文件，和make的Makefile很类似。

```rule

rule demo
  command = echo "this is a demo of $foo"

build out: demo
  foo = bar

```

## 编写你自己的ninja文件

Ninja和`Make`非常相似。他__执行一个文件之间的依赖图，通过检测文件修改时间,运行必要的命令来更新你的构建目标__。

一个构建文件（默认文件名为：build.ninja）提供一个`rule（规则）表`——长命令的简短名称，和运行编译器的方式一下。同时，附带提供`build`（构建）语句列表，表明通过rule如何构建文件——哪条规则应用于哪个输入产生哪一个输出。

从概念上讲，`build`语句描述项目的依赖图；而`rule`语句描述当给定一个图的一条边时，如何生成文件。


### 语法示例

这是一个用于验证绝大部分语法的.ninja文件，将作为后续描述相关的示例。具体内容，如下：

```bash

cflags = -Wall

rule cc
  command = gcc $cflags -c $in -o $out

build foo.o: cc foo.c
```


### 变量

ninja支持为`字符串`声明简短可读的名字。一个声明的语法，如下：

```bash

cflags = -g
```
可以在`=`右边使用，并通过`$`进行引用（类似`shell`和`perl`的语法）。具体形式，如下：

```bash

rule cc
  command = gcc $cflags -c $in -o $out
```

变量还可以用`${in}`($和成对的大括号)来引用。

当给定变量的值不能被修改，只能覆盖（shadowed）时,变量更恰当的叫法是`绑定`（"bindings"）。

### rule 规则

规则为命令行声明一个简短的名称。他们由关键字`rule`和`一个规则名称`打头的行开始，然后紧跟着`一组带缩进格式的` variable = value行组成。

以上示例中声明了一个名为`cc`的rule，连同一个待运行的命令。在`rule`（规则）上下文中，`command`变量用于定义待执行的命令，`$in`展开（expands）为输入文件列表（foo.c）,而`$out`为命令的输出文件列表（foo.o）。[参考手册](http://martine.github.io/ninja/manual.html#ref_rule)中罗列了所有特殊的变量。


### buid 构建语句

`build`语句声明输入和输出文件之间的一个关系。构建语句由关键字`build`开头，格式为`build outputs: rulename inputs`。这样的一个声明，所有的输出文件`来源于`（derived from）输入文件。当缺输出文件或输入文件变更时，Ninja将会运行此规则来重新生成输出。

以上的简单示例，描述了使用cc规则如何构建foo.o文件。

在`build block`范围内（包括相关规则的执行），变量`$in`表示输入列表，`$out`表示输出列表。

一个构建语句，可以和rule一样，紧跟`一组带缩进格式的key = value对`。当在命令中变量执行时，这些变量将`覆盖`（shadow）任何变量。比如：

```bash

cflags = -Wall -Werror
rule cc
  command = gcc $cflags -c $in -o $out

# 如果没有制定，build的输出将是$cflags
build foo.o: cc foo.c

# 但是，你可以在特殊的build中覆盖cflags这样的变量
build special.o: cc special.c
  cflags = -Wall

# cflags变量仅仅覆盖了special.o的范围
# 以下的子序列build行得到的是外部的(原始的)cflags
build bar.o: cc bar.c
```

### 从代码中生成Ninja文件

Ninja发行包中的`misc/ninja_syntax.py`是一个很小的python模块，用于生成Ninja文件。你可以使用python，执行如`ninja.rule(name='foo', command='bar', depfile='$out.d')`的调用，生成合适的语法。如果这样还不错，可以将其整合到你的项目中。


## 更多细节


### phony 规则

可以使用特殊的规则`phony`，创建其他`target`（编译构建目标）的`别名`。比如：

```bash

build foo: phony some/file/in/a/faraway/subdir/foo
```

这样使得`ninja foo`构建更长的路径。从语义上讲，`phony`规则等同于一个没有做任何操作的普通规则，但是phony规则通过特殊的方式进行处理，这样当其运行时不会被打印，记日志，也不作为构建过程中打印出来的命令计数。

还可以用`phony`为构建时可能还不存在的文件创建`dummy`目标。

### default 目标语句

默认情况下，如果没有在命令行中指定`target`，那么Ninja将构建任何地方没有作为输入命名的每一个输出。可以通过`default`目标语句来重写这个行为。一个`default`语句，让Ninja构建一个给定的输出文件子集，如果命令行中没有指定`构建目标`。

默认目标语句，由关键字`default`打头，并且采用`default targets`的格式。__一个default目标语句必须出现在，声明这个目标作为一个输出文件的构建语句之后__。他们是累积的（cumulative），所以可以使用多个default语句来扩展默认目标列表。比如：

```bash

default foo bar
default baz
```

### Ninja构建日志

Ninja构建日志保存在构建过程的跟目录或.ninja文件中builddir变量对应的目录的`.ninja_log`文件中。


## C/C++头文件依赖

Ninja目前支持`depfile`和`deps`模式的C/C++头文件依赖生成。 如

```bash

rule cc
  depfile = $out.d
  command = gcc -MMD -MF $out.d [other gcc flags here]
```

`-MMD`标识告诉gcc要生成头文件依赖，`-MF`则说明要写到哪里。

deps按照编译器的名词来管理。具体如下：（针对微软的VC：msvc）

```bash

rule cc
  deps = msvc
  command = cl /showIncludes -c $in /Fo$out
```

## Pools

为了支持并发作业，Ninja还支持`pool`的机制（和用`-j`并行模式一样）。此处不详细描述了。具体示例，如下：

```bash

# No more than 4 links at a time.
pool link_pool
  depth = 4

# No more than 1 heavy object at a time.
pool heavy_object_pool
  depth = 1

rule link
  ...
  pool = link_pool

rule cc
  ...

# The link_pool is used here. Only 4 links will run concurrently.
build foo.exe: link input.obj

# A build statement can be exempted from its rule's pool by setting an
# empty pool. This effectively puts the build statement back into the default
# pool, which has infinite depth.
build other.exe: link input.obj
  pool =

# A build statement can specify a pool directly.
# Only one of these builds will run at a time.
build heavy_object1.obj: cc heavy_obj1.cc
  pool = heavy_object_pool
build heavy_object2.obj: cc heavy_obj2.cc
  pool = heavy_object_pool
The console pool
```


## 更加详细的语法

请阅读[参考手册](http://martine.github.io/ninja/manual.html#ref_rule)，此处只做概要说明。

一个ninja构建文件，由一系列的声明构成。一个声明可以是一个：

* __rule声明__，由`rule rulename`开头,然后紧跟一系列`带缩进的变量定义行`；

* __一个build边__,其格式为`build output1 output2: rulename input1 input2`。隐士依赖用`| dependency1 dependency2`表达；Order-only依赖用行末的`|| dependency1 dependency2`表达。

* __变量声明__，形如`variable = value`；

* __默认目标语句__，形如`default target1 target2`；

* __引入更多的文件__，形如`subninja path`或`include path`

* __一个pool声明__，形如`pool poolname`。


### 词法

Ninja仅支持ASCII字符集。

`注释`以为`#`开始一直到行末。

新行是很重要的。像` build foo bar`的语句，是一堆空格分割分词（token），到换行结束。一个分词中的`新行`和`空格`必须进行转译。

目前只有一个转译字符，`$`，其具有以下行为：

```bash

$ followed by a newline
```

转译`换行`，让当前行一直扩展到下一行。

```bash

$ followed by text
```

这是， 变量引用。

```bash

${varname}
```

这是，另`$varname`的另一种语法。

```bash

$ followed by space
```

这表示`一个空格`。(仅在path列表中，需要用空格分割文件名)

```bash

$:
```
这表示一个冒号。（仅在build行中需要。此时冒号终止输出列表）

```bash

$$
```

这个表示，字面值的`$`。

一个build或default语句，最先被解析，作为一个空格分割的文件名列表，然后每一个name都被展开。也就是说，变量中的一个空格将作为被展开后文件名中的一个空格。

```ninja

spaced = foo bar
build $spaced/baz other$ file: ...
# The above build line has two outputs: "foo bar/baz" and "other file".
```

在一个`name = value`语句中，value前的空白都会被去掉。出现跨行时，后续行起始的空白也会被去掉。

```ninja

two_words_with_one_space = foo $
    bar
one_word_with_no_space = foo$
    bar
```

其他的空白，仅位于`行开始`处的很重要。__如果一行的缩进比前一行多，那么被人为是其父边界的一部分。如果缩进比前一行少，那他就关闭前一个边界__。


### 顶层变量

Ninja支持的顶层变量有`builddir`和`ninja_required_version`。具体说明，如下：

* __builddir__: 构建的一些输出文件的存放目录。
* __ninja_required_version__:指定满足构建需求的最小Ninja版本。


### rule变量

一个rule块包含一个`key = value`的列表声明，这直接影响规则的处理。以下是一些特殊的key：

* command (required)： 待执行的命令。这个字符串（$variables被展开之后），被直接传递给`sh -c`，不经过Ninja翻译。每一个规则只能包含一条command声明。如果有多条命令，需要使用` &&`符号进行链接。

* depfile： 指向一个可选的Makefile，其中包含额外的`隐式依赖`。这个明确的为了支持C/C++的头文件依赖。

* deps: （1.3版本开始支持）如果存在，必须是gcc或msvc，来指定特殊的依赖。产生的数据库保存在`builddir`指定目录`.ninja_deps`文件中。

* msvc_deps_prefix： （1.5版本开始支持）定义必须从msvc的`/showIncludes`输出中去掉的字符串。仅在`deps = msvc`而且使用非英语的Visual Studio版本时使用。

* description： 命令的简短描述，作为命令运行时更好的打印输出。打印整行还是对应的描述，由`-v`标记控制。如果一个命令执行失败，整个命令行总是在命令输出之前打印。

* generator： 如果存在，指明这条规则是用来`重复调用`生成器程序。通过两种特殊的方式，处理使用生成器规则构建文件：首先，如果命令行修改了，他们不会重新构建；其次，默认不会被清除。

* in： 空格分割的文件列表被作为一个`输入`传递给引用此rule的构建行，如果出现在命令中需要使用`${in}`(shell-quoted)。（提供`$in`仅仅为了图个方便，如果你需要文件列表的子集或变种，请构建一个新的变量，然后传递新的变量。）

* __in_newline__： 和`$in`一样，只是分割符为`换行`而不是`空格`。（仅为了和__$rspfile_content__一起使用，解决`MSVC linker`使用固定大小的缓冲区处理输入，而造成的一个bug。）

* out： 空格分割的文件列表被作为一个`输出`传递给引用此rule的构建行，如果出现在命令中需要使用`${out}`；

* restat: 如果存在，引发Ninja在命令行执行完之后，重新统计命令的输出。

* __rspfile, rspfile_content__： 如果存在（两个同时），Ninja将为给定命令提供一个响应文件，比如，在调用命令之前将选定的字符串(rspfile_content)写到给定的文件（rspfile），命令执行成功之后阐述文件。

这个在`Windows`系统非常有用，因为此时命令行的最大长度非常受限，必须使用响应文件替代。具体使用方式，如下：

```ninja

rule link
  command = link.exe /OUT$out [usual link flags here] @$out.rsp
  rspfile = $out.rsp
  rspfile_content = $in

build myapp.exe: link a.obj b.obj [possibly many other .obj files]
```

### 构建依赖

Ninja目前支持3种类型的构建依赖。分别是：

* 罗列在build行中的`显式的依赖`。他们可以作为规则中的`$in`变量。这是标准依赖格式。

* 从`depfile`属性或构建语句末尾的`| dep1 dep2`语法获得的`隐式依赖`。这个和`显式依赖`一样，但是不能在`$in`中使用（不可见）。

* 通过构建行末`|| dep1 dep2`语法表示的`次序唯一（Order-only）依赖`。他们过期的时候，输出不会被重新构建，直到他们被重建，但`仅`修改这种依赖不会引发输出重建。


## 变量展开

变量在`路径`（在build或default语句）和`name = value`右边被展开。

当`name = value`语句被执行，右手边的被立即展开（根据以下的规则），从此`$name`扩展为被展开结果的静态字符串。永远也不会存在，你将需要使用双转译（"double-escape"）来保护一个值被第二次展开。

所有变量在解析过程，遇到的时候立即被展开，除了一个非常重要的例外：`rule块中的变量`仅在规则被使用的时候才被展开，而不是声明的时候。在以下的示例中，demo打印出"this is a demo of bar"而不是"this is a demo of $foo"。

```bash

rule demo
  command = echo "this is a demo of $foo"

build out: demo
  foo = bar
```

### 评估和边界


顶层（Top-level）变量声明的边界，是相关的文件。

`subninja`关键自，用于包含另一个`.ninja`文件，其表示新的边界。被包含的subninja文件可以使用父文件中的变量，在文件边界中覆盖他们的值，但是这不影响父文件中变量的值。

同时，可以用`#include`语句在当前边界内，引入另一个`.ninja`文件。这个有点像`C`中的`#include`语句。

构建块中声明的变量的边界，就是其所属的块。一个构建块中展开的变量的所有查询次序为：

* 特殊内建变量($in, $out)；
* build/rule块中构建层的变量；
* 构建行所在文件中的文件层变量（File-level）；
* 使用subninja关键字引入那个文件的（父）文件中的变量。


## 最后再看一下编译ninja的构建文件


```bash

# This file is used to build ninja itself.
# It is generated by configure.py.

ninja_required_version = 1.3

# The arguments passed to configure.py, for rerunning it.
configure_args = --platform=linux

builddir = build
cxx = g++
ar = ar
cflags = -g -Wall -Wextra -Wno-deprecated -Wno-unused-parameter -fno-rtti $
    -fno-exceptions -fvisibility=hidden -pipe $
    -Wno-missing-field-initializers '-DNINJA_PYTHON="python"' -O2 -DNDEBUG $
    -DUSE_PPOLL
ldflags = -L$builddir

rule cxx
  command = $cxx -MMD -MT $out -MF $out.d $cflags -c $in -o $out
  description = CXX $out
  depfile = $out.d
  deps = gcc

rule ar
  command = rm -f $out && $ar crs $out $in
  description = AR $out

rule link
  command = $cxx $ldflags -o $out $in $libs
  description = LINK $out

# browse_py.h is used to inline browse.py.
rule inline
  command = src/inline.sh $varname < $in > $out
  description = INLINE $out
build $builddir/browse_py.h: inline src/browse.py | src/inline.sh
  varname = kBrowsePy

build $builddir/browse.o: cxx src/browse.cc || $builddir/browse_py.h

# the depfile parser and ninja lexers are generated using re2c.
rule re2c
  command = re2c -b -i --no-generation-date -o $out $in
  description = RE2C $out
build src/depfile_parser.cc: re2c src/depfile_parser.in.cc
build src/lexer.cc: re2c src/lexer.in.cc

# Core source files all build into ninja library.
build $builddir/build.o: cxx src/build.cc
build $builddir/build_log.o: cxx src/build_log.cc
build $builddir/clean.o: cxx src/clean.cc
build $builddir/debug_flags.o: cxx src/debug_flags.cc
build $builddir/depfile_parser.o: cxx src/depfile_parser.cc
build $builddir/deps_log.o: cxx src/deps_log.cc
build $builddir/disk_interface.o: cxx src/disk_interface.cc
build $builddir/edit_distance.o: cxx src/edit_distance.cc
build $builddir/eval_env.o: cxx src/eval_env.cc
build $builddir/graph.o: cxx src/graph.cc
build $builddir/graphviz.o: cxx src/graphviz.cc
build $builddir/lexer.o: cxx src/lexer.cc
build $builddir/line_printer.o: cxx src/line_printer.cc
build $builddir/manifest_parser.o: cxx src/manifest_parser.cc
build $builddir/metrics.o: cxx src/metrics.cc
build $builddir/state.o: cxx src/state.cc
build $builddir/util.o: cxx src/util.cc
build $builddir/version.o: cxx src/version.cc
build $builddir/subprocess-posix.o: cxx src/subprocess-posix.cc
build $builddir/libninja.a: ar $builddir/browse.o $builddir/build.o $
    $builddir/build_log.o $builddir/clean.o $builddir/debug_flags.o $
    $builddir/depfile_parser.o $builddir/deps_log.o $
    $builddir/disk_interface.o $builddir/edit_distance.o $
    $builddir/eval_env.o $builddir/graph.o $builddir/graphviz.o $
    $builddir/lexer.o $builddir/line_printer.o $builddir/manifest_parser.o $
    $builddir/metrics.o $builddir/state.o $builddir/util.o $
    $builddir/version.o $builddir/subprocess-posix.o

# Main executable is library plus main() function.
build $builddir/ninja.o: cxx src/ninja.cc
build ninja: link $builddir/ninja.o | $builddir/libninja.a
  libs = -lninja

# Tests all build into ninja_test executable.
test_cflags = -g -Wall -Wextra -Wno-deprecated -Wno-unused-parameter $
    -fno-rtti -fno-exceptions -fvisibility=hidden -pipe $
    -Wno-missing-field-initializers -DNINJA_PYTHON="python" -O2 -DNDEBUG $
    -DUSE_PPOLL -DGTEST_HAS_RTTI=0
build $builddir/build_log_test.o: cxx src/build_log_test.cc
  cflags = $test_cflags
build $builddir/build_test.o: cxx src/build_test.cc
  cflags = $test_cflags
build $builddir/clean_test.o: cxx src/clean_test.cc
  cflags = $test_cflags
build $builddir/depfile_parser_test.o: cxx src/depfile_parser_test.cc
  cflags = $test_cflags
build $builddir/deps_log_test.o: cxx src/deps_log_test.cc
  cflags = $test_cflags
build $builddir/disk_interface_test.o: cxx src/disk_interface_test.cc
  cflags = $test_cflags
build $builddir/edit_distance_test.o: cxx src/edit_distance_test.cc
  cflags = $test_cflags
build $builddir/graph_test.o: cxx src/graph_test.cc
  cflags = $test_cflags
build $builddir/lexer_test.o: cxx src/lexer_test.cc
  cflags = $test_cflags
build $builddir/manifest_parser_test.o: cxx src/manifest_parser_test.cc
  cflags = $test_cflags
build $builddir/ninja_test.o: cxx src/ninja_test.cc
  cflags = $test_cflags
build $builddir/state_test.o: cxx src/state_test.cc
  cflags = $test_cflags
build $builddir/subprocess_test.o: cxx src/subprocess_test.cc
  cflags = $test_cflags
build $builddir/test.o: cxx src/test.cc
  cflags = $test_cflags
build $builddir/util_test.o: cxx src/util_test.cc
  cflags = $test_cflags
build ninja_test: link $builddir/build_log_test.o $builddir/build_test.o $
    $builddir/clean_test.o $builddir/depfile_parser_test.o $
    $builddir/deps_log_test.o $builddir/disk_interface_test.o $
    $builddir/edit_distance_test.o $builddir/graph_test.o $
    $builddir/lexer_test.o $builddir/manifest_parser_test.o $
    $builddir/ninja_test.o $builddir/state_test.o $
    $builddir/subprocess_test.o $builddir/test.o $builddir/util_test.o | $
    $builddir/libninja.a
  libs = -lninja -lgtest_main -lgtest -lpthread

# Ancillary executables.
build $builddir/build_log_perftest.o: cxx src/build_log_perftest.cc
build build_log_perftest: link $builddir/build_log_perftest.o | $
    $builddir/libninja.a
  libs = -lninja -lgtest_main -lgtest -lpthread
build $builddir/canon_perftest.o: cxx src/canon_perftest.cc
build canon_perftest: link $builddir/canon_perftest.o | $builddir/libninja.a
  libs = -lninja -lgtest_main -lgtest -lpthread
build $builddir/depfile_parser_perftest.o: cxx src/depfile_parser_perftest.cc
build depfile_parser_perftest: link $builddir/depfile_parser_perftest.o | $
    $builddir/libninja.a
  libs = -lninja -lgtest_main -lgtest -lpthread
build $builddir/hash_collision_bench.o: cxx src/hash_collision_bench.cc
build hash_collision_bench: link $builddir/hash_collision_bench.o | $
    $builddir/libninja.a
  libs = -lninja -lgtest_main -lgtest -lpthread
build $builddir/manifest_parser_perftest.o: cxx $
    src/manifest_parser_perftest.cc
build manifest_parser_perftest: link $builddir/manifest_parser_perftest.o | $
    $builddir/libninja.a
  libs = -lninja -lgtest_main -lgtest -lpthread

# Generate a graph using the "graph" tool.
rule gendot
  command = ./ninja -t graph all > $out
rule gengraph
  command = dot -Tpng $in > $out
build $builddir/graph.dot: gendot ninja build.ninja
build graph.png: gengraph $builddir/graph.dot

# Generate the manual using asciidoc.
rule asciidoc
  command = asciidoc -b docbook -d book -o $out $in
  description = ASCIIDOC $out
rule xsltproc
  command = xsltproc --nonet doc/docbook.xsl $in > $out
  description = XSLTPROC $out
build $builddir/manual.xml: asciidoc doc/manual.asciidoc
build doc/manual.html: xsltproc $builddir/manual.xml | doc/style.css
build manual: phony || doc/manual.html

# Generate Doxygen.
rule doxygen
  command = doxygen $in
  description = DOXYGEN $in
doxygen_mainpage_generator = src/gen_doxygen_mainpage.sh
rule doxygen_mainpage
  command = $doxygen_mainpage_generator $in > $out
  description = DOXYGEN_MAINPAGE $out
build $builddir/doxygen_mainpage: doxygen_mainpage README COPYING | $
    $doxygen_mainpage_generator
build doxygen: doxygen doc/doxygen.config | $builddir/doxygen_mainpage

# Regenerate build files if build script changes.
rule configure
  command = ${configure_env}python configure.py $configure_args
  generator = 1
build build.ninja: configure | configure.py misc/ninja_syntax.py

default ninja

# Packaging
rule rpmbuild
  command = misc/packaging/rpmbuild.sh
  description = Building rpms..
build rpm: rpmbuild

build all: phony ninja ninja_test build_log_perftest canon_perftest $
    depfile_parser_perftest hash_collision_bench manifest_parser_perftest
```

## 针对ninja的优化

Ninja是一块非常好的构建工具，其实也是一个特殊的编译器，其中有很多值得学习和借鉴的地方。比如，使用`re2c`将正则表达式编译为c代码（PHP也是用了这个工具，干了类似的事情），使用`graphviz`生成dot格式的依赖文件等等。当然，我不太喜欢其对`python`的依赖。安装了其他依赖工具和库之后，还需要安装`python`，否则没法编译ninja。经过分析之后，我为ninja定制了一个Makefile编译方案，同时修改了部分python文件，这样在没有python的情况下依然可以编译和使用ninja。如果需要使用`ninja -t browse`和构建ninja_test等测试目标，那还是需要安装python。当然，要去掉这些依赖也不是很难的事情，如果需要哪天有空我可能就将其修改了。修改后的版本一贯的放在[github](https://github.com/guiquanz/ninja)上，需要的自取。仅将此文作为学习ninja的一个阶段性总结。欢迎交流和反馈。


## 扩展阅读

* [ninja manual](http://martine.github.io/ninja/manual.html)
* [ninja源码库](https://github.com/martine/ninja)
* [本文作者修改的分支](https://github.com/guiquanz/ninja)


## 祝大家玩的开心

## 编程之道，就在[编程之美]

![编程之美](/img/weixin_qr.jpg)


