--- 
layout: post
title: Facebook Folly C++11组件库简介及安装
date: 2012-12-03
categories:
  - 技术
tags:
  - Folly
  - C++
---

## 缘由

**仅针对 Fedora 17 x86-64平台, g++ 4.7.2环境**

![](/img/article/facebook.jpg)

[Folly](https://github.com/facebook/folly)是,Facebook于2012年6月初开源的一个基于C++11的C++组件库，提供了类似Boost库和std库的功能，包括散列、字符串、向量、内存分配、位处理等，以满足大规模高性能的需求，对Boost和std进行很好的补充和增强。

Folly的主要作者为C++大N人[Andrei Alexandrescu](http://www.erdani.com/)。Folly主要进行了速度上的提高、内存优化，以及数据结构类型的丰富。Folly由分布在61个头文件（还有20个cpp实现文件），40多个组件构成，涉及内存管理（Arena等）、高性能的数据结构（string和vector等）、实用数据结构（延时队列）、线程相关的优化（线程本地内存、旋转锁）等。

Folly在github的地址，如下：
[https://github.com/facebook/folly](https://github.com/facebook/folly)


## folly库安装及测试

1、安装folly库

由于folly是c++11的库，需要编译器支持相关功能特性，否则将无法编译通过（目前很多编译器都不支持c++11,所以folly不具备跨平台的通用性）。g++版本序满足>= 4.6.0才可以满足编译要求。具体的安装流程，如下：

特殊说明：

（1）、以下部署使用的是作者fork的folly库分支。主要变更，如下：
    - 增加了 autogen.sh 文件，方便生成各Makefile文件；
    - 修正了 folly/test/Makefile.am 中存在的问题（会造成 make check 失败）；
    - 提示：可以克隆原始的库，然后根据作者的fork分支进行相应的修改。

（2）、安装时，需要先安装gflags库，然后安装google-glog库
    - 如果安装顺序反了，执行 make check 时 VLOG 会报错；
    - 需要使用最新的版本，否则可能会出现API版本不兼容问题。 作者使用yum安装gflags和google-glog时，就出现此问题。

```cpp

#安装依赖工具
sudo yum install gcc gcc-c++ autoconf autoconf-archive automake libtool
sudo yum install boost-devel scons

#创建本地操作目录
mkdir -p ~/folly
cd ~/folly

#安装google的gflags库
svn checkout http://gflags.googlecode.com/svn/trunk/ gflags
cd gflags
./configure
make 
sudo make install

#安装google-glog库
cd ..
svn checkout http://google-glog.googlecode.com/svn/trunk/ google-glog
cd google-glog
./configure
make 
sudo make install

#克隆folly库
cd ..
git clone git://github.com/guiquanz/folly.git folly

#克隆并安装double-conversion库
git clone https://code.google.com/p/double-conversion/ double-conversion
cd double-conversion
cp ../folly/folly/SConstruct.double-conversion . 
scons -f SConstruct.double-conversion

#部署googletest库
cd ../folly/folly/test
wget http://googletest.googlecode.com/files/gtest-1.6.0.zip
unzip gtest-1.6.0.zip

#编译及安装folly
cd ..
sh autogen.sh
LDFLAGS=-L../../double-conversion CPPFLAGS=-I../../double-conversion/src ./configure
make 
sudo make install

```

2、执行folly回归测试

安装好folly库之后，可以通过以下方式执行其回归测试：

```cpp

cd ~/folly/folly/folly
make check
```

3、自己动手，写个测试程序

现在我们可以动过手写一个程序，简单测试一下：

（1）、编写测试程序

```cpp

mkdir -p ~/folly/folly/examples
cd ~/folly/folly/examples
```

```cpp

cat <<EOF > fbstring_test.cc
#include <folly/FBString.h>
#include <folly/Conv.h>
#include <iostream>

using namespace folly;
using namespace std;

int
main(void)
{
  fbstring str("Hello ");
  toAppend("Facebook Folly fbstring.", &str);

  cout << str << endl;

  return 0;
}
EOF
```

```cpp

cat <<EOF > Makefile
# Makefile for Fedora platform
# Modify it, if needed

DCPIC_BASE_DIR=../../double-conversion
LIBDCPIC_DIR= $(DCPIC_BASE_DIR)
LIBDCPIC_INCS= $(DCPIC_BASE_DIR)/src

CXX= g++
CXXFLAGS= -std=c++11 -O2 -I. -I$(LIBDCPIC_INCS)
SYSLIB= -L$(LIBDCPIC_DIR)


CXX_FILES= \
	fbstring_test.cc

O_FILES= $(CXX_FILES:.cc=.o)


TESTS= \
	 fbstring_test


all: $(TESTS)

test: all 
	for t in $(TESTS); do echo "**** Runing $$t"; ./$$t || exit 1; done


fbstring_test: fbstring_test.o
	$(CXX) -o $@ $< $(SYSLIB)


%.o: %.cc
	$(CXX) -c -o $@ $(CXXFLAGS) $< 


.PHONY: clean test

clean:
	rm -f $(TESTS) *.o *~

EOF
```

（2）、编译、执行程序

```cpp

make test

命令输出，如下：
g++ -c -o fbstring_test.o -std=c++11 -O2 -I. -I../../double-conversion/src fbstring_test.cc 
g++ -o fbstring_test fbstring_test.o -L../../double-conversion
for t in fbstring_test; do echo "**** Runing $t"; ./$t || exit 1; done
**** Runing fbstring_test
Hello Facebook Folly fbstring.
```


## 扩展阅读

1、QQ客户端团队博客中[“facebook开源库folly介绍”](http://impd.tencent.com/?p=278)一文；

2、[揭秘Facebook官方底层C++函数Folly](http://developer.51cto.com/art/201206/340607.htm)一文;

3、[Facebook Folly源代码分析](http://www.programmer.com.cn/12584/)(《程序员》杂志，2012年7-8期);

4、Folly项目[docs](https://github.com/guiquanz/folly/blob/master/folly/docs/Overview.md)目录下自带的文档。


