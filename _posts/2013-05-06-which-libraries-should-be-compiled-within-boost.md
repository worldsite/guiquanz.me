---
layout: post
title: Boost中哪些库，编译之后才能使用
date: 2013-05-06
categories:
  - 技术
tags:
  - Boost
---
## 缘由

![](/img/article/2013-05/01-01.png)

[Boost](http://www.boost.org/)是，一个宝库，是现代C++编程必备的库。其中，有些库是不需要编译的，直接包含头文件就可以使用，如`smart_ptr`等，而有些库在使用之前是需要先进行编译的，然后通过链接方式引入到用户代码中，如`date_time`等。那么到底哪些库，在使用之前需要事先编译呢？

其实，在boost根目录下执行`./bjam --show-libraris`命令，就可以知道当前版本的boost到底哪些库，在使用之前需要事先编译了。以`boost_1_53_0`为例，需要事先编译的库，如下：

    i@home boost_1_53_0> ./bjam --show-libraris
     
    Building the Boost C++ Libraries.
    
    
    Performing configuration checks
    
        - 32-bit                   : no
        - 64-bit                   : yes
        - x86                      : yes
        - has_icu builds           : no
    warning: Graph library does not contain MPI-based parallel components.
        note: to enable them, add "using mpi ;" to your user-config.jam
        - iconv (libc)             : yes
        - icu                      : no
        - icu (lib64)              : no
        - gcc visibility           : yes
        - long double support      : yes
    warning: skipping optional Message Passing Interface (MPI) library.
    note: to enable MPI support, add "using mpi ;" to user-config.jam.
    note: to suppress this message, pass "--without-mpi" to bjam.
    note: otherwise, you can safely ignore this message.
    
    Component configuration:
    
        - atomic                   : building
        - chrono                   : building
        - context                  : building
        - date_time                : building
        - exception                : building
        - filesystem               : building
        - graph                    : building
        - graph_parallel           : building
        - iostreams                : building
        - locale                   : building
        - math                     : building
        - mpi                      : building
        - program_options          : building
        - python                   : building
        - random                   : building
        - regex                    : building
        - serialization            : building
        - signals                  : building
        - system                   : building
        - test                     : building
        - thread                   : building
        - timer                    : building
        - wave                     : building
    
    ...patience...
    ...patience...
    ...patience...
    ...patience...
    ...found 8869 targets...
        
        
    The Boost C++ Libraries were successfully built!
    
    The following directory should be added to compiler include paths:
    
        /home/i/boost/boost_1_53_0
    
    The following directory should be added to linker library paths:
    
        /home/i/boost/boost_1_53_0/stage/lib


从命令输出结果可知，和操作系统相关的库都需要事先进行编译。如，math，thread和signals等。


## 扩展阅读


## 祝大家玩的开心


