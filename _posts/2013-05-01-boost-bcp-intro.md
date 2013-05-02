---
layout: post
title: bcp，只为boost库瘦身
date: 2013-05-01
categories:
  - 技术
tags:
  - Boost
  - bcp 
---
## Boost简介

![](/img/article/2013-05/01-01.png)

[Boost](http://www.boost.org/)，是一组经过同行评审的（peer-reviewed）可移植的C++代码库。采用Boost Software License进行开源，其作者很多都来自C++标准委员会。Boost库已成为C++的TR1标准之一，同时实现了很多C++11的功能（号称C++11的准标准库）。

Boost提供了很多高级的算法，数据结构等组件，应用非常广泛，如mongodb就采用了很多boost组件。陈硕的muduo库，也采用了boost的很多库……。其中，应用最广泛的，如array，call_traits等。

虽然，Boost很好，但不是每一个组件都是最佳实践（编码风格等），不是每一个库都很好用，不是每一个都达到产品级的质量要求。以下是[Google C++ Style Guide](http://google-styleguide.googlecode.com/svn/trunk/cppguide.xml#Boost)3.245版本，针对Boost库的使用规范：

__Boost__

`Use only approved libraries from the Boost library collection.`

`Definition`:

The Boost library collection is a popular collection of peer-reviewed, free, open-source C++ libraries.

`Pros`:

Boost code is generally very high-quality, is widely portable, and fills many important gaps in the C++ standard library, such as type traits, better binders, and better smart pointers. It also provides an implementation of the TR1 extension to the standard library.

`Cons`:

Some Boost libraries encourage coding practices which can hamper readability, such as metaprogramming and other advanced template techniques, and an excessively "functional" style of programming.

`Decision`:

In order to maintain a high level of readability for all contributors who might read and maintain code, we only allow an approved subset of Boost features. Currently, the following libraries are permitted:

    Call Traits from boost/call_traits.hpp
    Compressed Pair from boost/compressed_pair.hpp
    Pointer Container from boost/ptr_container except serialization and wrappers for containers not in the C++03  standard (ptr_circular_buffer.hpp and ptr_unordered*)
    Array from boost/array.hpp
    The Boost Graph Library (BGL) from boost/graph, except serialization (adj_list_serialize.hpp) and parallel/distributed algorithms and data structures (boost/graph/parallel/* and boost/graph/distributed/*).
    Property Map from boost/property_map, except parallel/distributed property maps (boost/property_map/parallel/*).
    The part of Iterator that deals with defining iterators: boost/iterator/iterator_adaptor.hpp, boost/iterator/iterator_facade.hpp, and boost/function_output_iterator.hpp
    The part of Polygon that deals with Voronoi diagram construction and doesn't depend on the rest of Polygon: boost/polygon/voronoi_builder.hpp, boost/polygon/voronoi_diagram.hpp, and boost/polygon/voronoi_geometry_type.hpp

We are actively considering adding other Boost features to the list, so this rule may be relaxed in the future.

另外，一个关键性的问题就是Boost标准发行版本实在太大了。解压之后大概400多M，如果有完整编译，需要1.2G左右的磁盘空间。虽然，有一小部分组件或实用功能不需要（Boost几乎就是template的乐高积木）编译，但这依然是阻碍很多人使用的一个原因。还好，Boost为了方便开发人员，组织和发布属于自己的独立组件，而提供了一个bcp工具（`估计很多人都不知道`）。当然，任何人都可以通过bcp来裁剪boost，以满足自己的需求（mongodb使用的，就是经过裁剪的boost库）。bcp才是本文的主角，下面言归正传。


## bcp实用工具

bcp是一个专门用于提取Boost子集组件的工具。对于想要从Boost独立发布个人的库代码的Boost开发者，以及想根据自己的需求裁剪/定制Boost的用户来说，这是一个非常有用的工具。

bcp同时可以报告你的代码依赖Boost组件以及这些组件使用的licences。

### bcp语法
    
    bcp --list [options] module-list
    
列出module-list及其所有依赖的文件列表。
    
    bcp [options] module-list output-path
     
拷贝module-list依赖的所有文件到output-path目录。
     
    bcp --report [options] module-list html-file
     
输出module-list的所有依赖到html报告中。其中依赖的文件，包括：licenses、源代码和测试程序代码等。
命令选项，包括:

    --boost=path
    
设置boost代码树本地路径。如果没有指定此选项，默认会将`当前路径`作为Boost代码树的`跟目录`。
    
    --scan
    
将module列表（module list）作为非boost文件，通过扫描确定对Boost的依赖，不会拷贝或列出模块列表自身相关的文件。
    
    --svn
    
仅仅拷贝处于svn版本管理之下的文件。

    --unix-lines
     
确保所有被拷贝的文件都采用unix风格的行编辑模式（line endings）。
    
    module-list: 
    
当`没有`指定`--scan`选项时，用于指定待拷贝的boost文件或库名列表。这个列表可以是：

    一个工具的名称：如`build`，将找到`tools/build`；
    一个库的名称：如`regex`；
    一个头文件的title：如`scoped_ptr`，将找到`boost/scoped_ptr.hpp`；
    一个头文件的文件名：如`scoped_ptr.hpp`，将找到`boost/scoped_ptr.hpp`；
    一个文件名：如`boost/regex.hpp`

当指定了`--scan`选项时，是一个非Boost（non-boost）的文件列表，用于扫描其对boost的依赖，这些文件将不会被拷贝或罗列。
    
    output-path
    
目标文件的输出目录。


### bcp使用示例
    
    bcp scoped_ptr /foo
    
拷贝`boost/scoped_ptr.hpp`及其依赖的源码到`/foo`目录。
     
    bcp boost/regex.hpp /foo
    
拷贝`boost/regex.hpp`和所有依赖的代码，包括regex源码（libs/regex/src）和构建文件（libs/regex/build）到`/foo`目录。但是，不会拷贝regex文档，test,或示/样例代码。
     
    bcp regex /foo
     
拷贝`所有`regex库（libs/regex），包括依赖的代码（比如，regex测试程序依赖的boost.test源码）到`/foo`。
    
    bcp regex config build /foo
    
拷贝`所有`regex库（libs/regex），附加config库（libs/config）和构建系统（tools/build）以及所有的依赖代码到`/foo`。（`这个非常有用`）
    
    bcp --scan --boost=/boost foo.cpp bar.cpp boost
    
   扫描`非boost`文件foo.cpp和bar.cpp对boost的依赖代码。同时，将其拷贝到子目录boost下。（`在不明确依赖关系的情况下，这个模式非常有用`）
    
    bcp --report regex.hpp boost-regex-report.html
     
为Boost模块regex.hpp创建一个名为boost-regex-report.html的HTML报告文件。此报告包含，license信息，作者明细和文件依赖。


## bcp应用演示

### 编译boost及bcp

1、下载 boost_1_53_0代码到`~/boost`目录，并进行本地编译

    i@home boost_1_53_0> cd ~/boost
    i@home boost> tar xzvf boost_1_53_0.tar.gz
    i@home boost_1_53_0> cd boost_1_53_0
    i@home boost_1_53_0> ./bootstrap.sh
    i@home boost_1_53_0> ./b2
    i@home boost_1_53_0> ./bjam --toolset=gcc stage
      
    i@home boost_1_53_0> cd tools/bcp
    i@home bcp> ../../bjam --toolset=gcc stage


2、定制boost/timer.hpp对应的依赖代码
    
    i@home bcp> cd ../..
    i@home boost_1_53_0> mkdir -p foo
    i@home boost_1_53_0> dist/bin/bcp boost/timer.hpp foo
     Copying file: boost/config.hpp
     Copying file: boost/config/abi/borland_prefix.hpp
     Copying file: boost/config/abi/borland_suffix.hpp
     Copying file: boost/config/abi/msvc_prefix.hpp
     Copying file: boost/config/abi/msvc_suffix.hpp
     Copying file: boost/config/abi_prefix.hpp
     Copying file: boost/config/abi_suffix.hpp
     Copying file: boost/config/auto_link.hpp
     Copying file: boost/config/compiler/borland.hpp
     Copying file: boost/config/compiler/clang.hpp
     Copying file: boost/config/compiler/codegear.hpp
     Copying file: boost/config/compiler/comeau.hpp
     Copying file: boost/config/compiler/common_edg.hpp
     Copying file: boost/config/compiler/compaq_cxx.hpp
     Copying file: boost/config/compiler/cray.hpp
     Copying file: boost/config/compiler/digitalmars.hpp
     Copying file: boost/config/compiler/gcc.hpp
     Copying file: boost/config/compiler/gcc_xml.hpp
     Copying file: boost/config/compiler/greenhills.hpp
     Copying file: boost/config/compiler/hp_acc.hpp
     Copying file: boost/config/compiler/intel.hpp
     Copying file: boost/config/compiler/kai.hpp
     Copying file: boost/config/compiler/metrowerks.hpp
     Copying file: boost/config/compiler/mpw.hpp
     Copying file: boost/config/compiler/nvcc.hpp
     Copying file: boost/config/compiler/pathscale.hpp
     Copying file: boost/config/compiler/pgi.hpp
     Copying file: boost/config/compiler/sgi_mipspro.hpp
     Copying file: boost/config/compiler/sunpro_cc.hpp
     Copying file: boost/config/compiler/vacpp.hpp
     Copying file: boost/config/compiler/visualc.hpp
     Copying file: boost/config/no_tr1/cmath.hpp
     Copying file: boost/config/no_tr1/complex.hpp
     Copying file: boost/config/no_tr1/functional.hpp
     Copying file: boost/config/no_tr1/memory.hpp
     Copying file: boost/config/no_tr1/utility.hpp
     Copying file: boost/config/platform/aix.hpp
     Copying file: boost/config/platform/amigaos.hpp
     Copying file: boost/config/platform/beos.hpp
     Copying file: boost/config/platform/bsd.hpp
     Copying file: boost/config/platform/cray.hpp
     Copying file: boost/config/platform/cygwin.hpp
     Copying file: boost/config/platform/hpux.hpp
     Copying file: boost/config/platform/irix.hpp
     Copying file: boost/config/platform/linux.hpp
     Copying file: boost/config/platform/macos.hpp
     Copying file: boost/config/platform/qnxnto.hpp
     Copying file: boost/config/platform/solaris.hpp
     Copying file: boost/config/platform/symbian.hpp
     Copying file: boost/config/platform/vms.hpp
     Copying file: boost/config/platform/vxworks.hpp
     Copying file: boost/config/platform/win32.hpp
     Copying file: boost/config/posix_features.hpp
     Copying file: boost/config/requires_threads.hpp
     Copying file: boost/config/select_compiler_config.hpp
     Copying file: boost/config/select_platform_config.hpp
     Copying file: boost/config/select_stdlib_config.hpp
     Copying file: boost/config/stdlib/dinkumware.hpp
     Copying file: boost/config/stdlib/libcomo.hpp
     Copying file: boost/config/stdlib/libcpp.hpp
     Copying file: boost/config/stdlib/libstdcpp3.hpp
     Copying file: boost/config/stdlib/modena.hpp
     Copying file: boost/config/stdlib/msl.hpp
     Copying file: boost/config/stdlib/roguewave.hpp
     Copying file: boost/config/stdlib/sgi.hpp
     Copying file: boost/config/stdlib/stlport.hpp
     Copying file: boost/config/stdlib/vacpp.hpp
     Copying file: boost/config/suffix.hpp
     Copying file: boost/config/user.hpp
     Copying file: boost/config/warning_disable.hpp
     Copying file: boost/detail/endian.hpp
     Copying file: boost/detail/limits.hpp
     Copying file: boost/limits.hpp
     Copying file: boost/non_type.hpp
     Copying file: boost/timer.hpp
     Copying file: boost/type.hpp
     Copying file: boost/version.hpp
     no errors detected

## 扩展阅读

* [The bcp utility](http://www.boost.org/doc/libs/1_35_0/tools/bcp/bcp.html)

## 祝大家玩的开心

