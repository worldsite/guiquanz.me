---
layout: post
title: SystemTap，万能的性能测量和调式诊断工具
date: 2013-04-21
categories:
  - 技术
tags:
  - SystemTap
---
## SystemTap简介

[![](/img/article/2013-04/21-01.jpg)](http://sourceware.org/systemtap/index.html)

[SystemTap](http://sourceware.org/systemtap/index.html) provides free software (GPL) infrastructure to simplify the gathering of information about the running Linux system. This assists diagnosis of a performance or functional problem. SystemTap eliminates the need for the developer to go through the tedious and disruptive instrument, recompile, install, and reboot sequence that may be otherwise required to collect data.

SystemTap provides a simple command line interface and scripting language for writing instrumentation for a live running kernel plus user-space applications. We are publishing samples, as well as enlarging the internal "tapset" script library to aid reuse and abstraction.

Among other tracing/probing tools, SystemTap is the tool of choice for complex tasks that may require live analysis, programmable on-line response, and whole-system symbolic access. SystemTap can also handle simple tracing jobs.

Current project members include Red Hat, IBM, Hitachi, and Oracle.


SystemTap的数据流图:

![](/img/article/2013-04/21-02.jpg)

## 软件安装及使用

__以下操作针对 Fedora 平台__

（1）安装 systemtap
    
    sudo yum install systemtap systemtap-runtime

或者`源码安装`：
    
    git clone git://sourceware.org/git/systemtap.git
    git clone git://git.fedorahosted.org/git/elfutils.git
    cd systemtap
    ./configure –with-elfutils=../elfutils


（2）安装依赖的内核包

```text

     取系统的内核版本： `uname -r`
     安装内核包： 如我的是`3.8.4-102.fc17.x86_64`，需要安装以下的包：
```
    kernel-3.8.4-102.fc17.x86_64
    kernel-devel-3.8.4-102.fc17.x86_64
    kernel-debuginfo-common-x86_64-3.8.4-102.fc17.x86_64
    kernel-debuginfo-3.8.4-102.fc17.x86_64

安装命令，如下：
        
    sudo yum install kernel-devel-3.8.4-102.fc17.x86_64
    sudo debuginfo-install kernel-debuginfo-3.8.4-102.fc17.x86_64
    sudo debuginfo-install kernel-debuginfo-common-x86_64-3.8.4-102.fc17.x86_64

或者，先安装systemtap，然后执行stap-prep确认还需要安装的其他组建：
    
    yum install systemtap
    stap-prep

*** `systemtap` 目前主要用于调查和监控内核空间`内核函数`，`系统函数调用`和其他各类事件。用户空间的事件探测依赖于`Utrace`机制，但是很多Linux发行版的内核都不支持。***


### 应用示例

（1）简单测试例子
    
    sudo stap -ve 'probe begin { log("hello world") exit() }'

输出结果，如下：

<pre class="prettyprint linenums">
i@home> sudo stap -ve 'probe begin { log("hello world") exit() }'
[sudo] password for ruby: 
Pass 1: parsed user script and 126 library script(s) using 269320virt/90464res/2976shr/88424data kb, in 620usr/80sys/1789real ms.
Pass 2: analyzed script: 1 probe(s), 2 function(s), 0 embed(s), 0 global(s) using 272620virt/93876res/3180shr/91724data kb, in 50usr/10sys/133real ms.
Pass 3: using cached /root/.systemtap/cache/8a/stap_8a7070bd8d437653873bd58f9caa162b_1005.c
Pass 4: using cached /root/.systemtap/cache/8a/stap_8a7070bd8d437653873bd58f9caa162b_1005.ko
Pass 5: starting run.
`hello world`
Pass 5: run completed in 10usr/30sys/620real ms.
</pre>

其中，`Pass 5:`开始的3行输出表明：

SystemTap可以成功创建指令探测内核，运行内核，检测被探测的事件（例如，虚拟文件系统的`读`事件），然后执行一个合法的处理操作（打印`hello world`，然后退出）。


（2）编写一个c程序，然后进行探测

<pre class="prettyprint linenums">
#include &lt;stdio.h&gt;
#include &lt;stdlib.h&gt;

int
main(void)
{
    printf("Hello, World!\n");
    return 0;
}
</pre>

__编译： 一定要加`-g`，一切都基于调试信息__

<pre class="prettyprint linenums">
gcc -g -o test test.c
</pre>

__编写stap脚本__: `./test程序main函数被调用时，打印一个提示信息`

    sudo stap -e 'probe process("/home/i/stap/test").function("main") { printf("probe ok\n"); }'

然后，在另一个tty中执行`/home/i/stap/test`程序，会在stap的tty中打印`probe ok`字符串。


## SystemTap的实现原理

![](/img/article/2013-04/21-03.gif)


其实，SystemTap的工作原理还是很简单的：
解析stap文件，生成对应的c代码，然后将其编译为一个内核模块，并加载到内核中，当事件发生时执行相关的探测操作，输出侦探结果。这个流程，还可以分步执行的：

    Systemtap works by translating the script to C, running the system C compiler to create a kernel module
from that. When the module is loaded, it activates all the probed events by hooking into the kernel. Then,
as events occur on any processor, the compiled handlers run. Eventually, the session stops, the hooks are
disconnected, and the module removed.

（1）从stap脚本，编译生成内核模块

    stap -r <kernel_version> <script> -m <module_name>

其中，`kernel_version`: 内核版本号，`uname -r`的值

`script`: 输入的stap脚本名称

`module_name`: 输出的内核模块的名称

如，

    sudo stap -r 3.8.4-102.fc17.x86_64 test.stp -m test

将从`test.stp`文件，生成`test.ko`模块。

（2）加载内核模块: 运行探测操作

    staprun <module_name>.ko

其中，`module_name`为内核模块的名称：

如，
    sudo staprun -r test.ko

__注意：因为stap执行时，需要访问内核空间，需要root权限。如果想让普通用户也有执行stap权限，可以将其加入`stapdev`或`stapuser`用户组__

```shell

[ruby@Sudoku python-in-action]$ sudo stap -F test.stp
[sudo] password for ruby: 

Disconnecting from systemtap module.
To reconnect, type "staprun -A stap_8a7070bd8d437653873bd58f9caa162b_1331"

[ruby@Sudoku python-in-action]$ sudo staprun -A stap_8a7070bd8d437653873bd58f9caa162b_1331
hello world
```


### 内建的探测点(部分）：

![](/img/article/2013-07/27-01.png)

如，

    probe kernel.function("*@net/socket.c") { }
    probe kernel.function("*@net/socket.c").return { }

### 内建的探测内容（部分）：

![](/img/article/2013-07/27-02.png)



## 扩展阅读

* agentzh，针对Nginx-Lua调试的工具集[nginx-systemtap-toolkit](https://github.com/agentzh/nginx-systemtap-toolkit)
* agentzh，systemtap宏扩展语言[stapxx](https://github.com/agentzh/stapxx)
* [Introduction to SystemTap A pratical approach](http://raisama.net/talks/fisl10/kernel-hacking/stap.pdf)
* [SystemTap: Instrumenting the Linux Kernelfor Analyzing Performance and Functional Problems](http://www.redbooks.ibm.com/redpapers/pdfs/redp4469.pdf)
* [Linux Performance and Tuning Guidelines](http://www.redbooks.ibm.com/redpapers/pdfs/redp4285.pdf)
* [systemtap主页](http://sourceware.org/systemtap/index.html)
* [SystemTap Tapset Reference For SystemTap in Red Hat Enterprise Linux 6]()
* [SystemTap/DTrace with MySQL & Drizzle](http://cdn.oreillystatic.com/en/assets/1/event/36/Monitoring%20Drizzle%20or%20MySQL%20With%20DTrace%20and%20SystemTap%20Presentation.pdf)
* [Various SystemTap scripts for MySQL and Drizzle](http://github.com/posulliv/stap)
* [SystemTap Wiki](http://sourceware.org/systemtap/wiki/HomePage)

* [Brendan's blog:Flame Graphs](http://dtrace.org/blogs/brendan/2011/12/16/flame-graphs/)
* [Brendan's blog: Off-CPU Performance Analysis](http://dtrace.org/blogs/brendan/2011/07/08/off-cpu-performance-analysis/)
* [Brendan's blog: Using SystemTap](http://dtrace.org/blogs/brendan/2011/10/15/using-systemtap/)

* [Linux下如何知道文件被那个进程写](http://rdc.taobao.com/blog/cs/?p=1758)
* [A guide on how to install Systemtap on an Ubuntu system](http://sourceware.org/systemtap/wiki/SystemtapOnUbuntu)


## 祝大家玩的开心


