---
layout: post
title: Google Test小试
date: 2013-03-23
categories:
  - 技术
tags:
  - Google Test
---
## Google Test简介

[Google Test](http://code.google.com/p/googletest/)，是一块不错的C/C++单元测试软件，流行度很高。在[LLVM](http://llvm.org/)、[ProtoBuf](http://code.google.com/p/protobuf/)以及[Chromium](http://www.chromium.org/)等项目中有使用。

本文将介绍`Google Test`的安装及简单测试。


## Google Test安装

`Google Test`支持很多种安装模式，此仅介绍几种基于代码库最新版本代码的安装方式。具体，如下：

0、下载源代码：依赖于`svn`工具

    mkdir -p ~/googletest && cd ~/googletest
    svn checkout http://googletest.googlecode.com/svn/trunk/ googletest


1、一般的安装方式：编译生成静态库文件，不编译测试代码

仅包含`${GTEST_DIR}/include`，编译`${GTEST_DIR}/src/gtest-all.cc`，打包为一个静态库libgtest.a文件。然后，将libgtest.a链接到你的测试代码中。针对Linux平台，g++的示例，如下：

    g++ -isystem ${GTEST_DIR}/include -I${GTEST_DIR} \
      -pthread -c ${GTEST_DIR}/src/gtest-all.cc
    ar -rv libgtest.a gtest-all.o

**注意**： 测试多线程代码时需要加 -pthread

利用静态库，编译你的测试代码：

    g++ -isystem ${GTEST_DIR}/include -pthread path/to/your_test.cc libgtest.a \
      -o your_test

**注意**：`-isystem ${GTEST_DIR}/include`的作用是，将`${GTEST_DIR}/include`目录作为编译器头文件检索路径。


2、通过cmake安装：依赖于cmake

    cd googletest
    mkdir build
    cd build
    cmake .. 或 cmake -Dgtest\_build_samples=ON .. (编译测试例子)
    make && make install


## 将Google Test作为自己项目的测试组件

既可以通过标准模式安装Google Test，然后在自己的代码中使用其头文件（#include &lt;gtest/gtest.h&gt;）编写测试代码，也可将Google Test作为自己项目的一个子目录，测试时直接编译其代码，这样更便于代码发布。具体操作，如下： 
将`${GTEST_DIR}/include`和`${GTEST_DIR}/src`包含到自己的项目中，然后参考`${GTEST_DIR}/make/Makefile`文件，编写你的编译方式即可。
示例编译，如下：

    cd ${GTEST_DIR}/make
    make
    ./sample1_unittest

**注意**： 以上说明中的`${GTEST_DIR}`为Google Test的源码根目录。
针对文为`~/googletest/googletest/`目录。


## 测试

以样例sample1为例，其执行结果，如下：

[ruby@home samples]$ ./sample1_unittest

![sample1_unittest](/img/article/2013-03/23-02.png)


## 编写基于Google Test的测试代码

以样例sample1为例。相关代码，如下：

`${GTEST_DIR}/samples/sample1.h`

`${GTEST_DIR}/samples/sample1.cc`

`${GTEST_DIR}/samples/sample1_unittest.cc`


其中，`sample1.h`的定义，如下：

    #ifndef GTEST\_SAMPLES\_SAMPLE1_H_
    #define GTEST\_SAMPLES\_SAMPLE1_H_
     
    // Returns n! (the factorial of n).  For negative n, n! is defined to be 1.
    int Factorial(int n);
     
    // Returns true iff n is a prime number.
    bool IsPrime(int n);
     
    #endif  // GTEST\_SAMPLES\_SAMPLE1_H_


其中，`sample1.cc`的实现，如下：
    
    #include "sample1.h"
    
    // Returns n! (the factorial of n).  For negative n, n! is defined to be 1.
    int Factorial(int n) {
      int result = 1;
      for (int i = 1; i <= n; i++) {
        result *= i;
      }
    
      return result;
    }
    
    // Returns true iff n is a prime number.
    bool IsPrime(int n) {
      // Trivial case 1: small numbers
      if (n <= 1) return false;
        
      // Trivial case 2: even numbers
      if (n % 2 == 0) return n == 2;
    
      // Now, we have that n is odd and n >= 3.
    
      // Try to divide n by every odd number i, starting from 3
      for (int i = 3; ; i += 2) {
        // We only have to try i up to the squre root of n
        if (i > n/i) break;
    
        // Now, we have i <= n/i < n.
        // If n is divisible by i, n is not prime.
        if (n % i == 0) return false;
      }
    
      // n has no integer factor in the range (1, n), and thus is prime.
      return true;
    }
    

其中，测试代码`sample1_unittest.cc`实现，如下：

    // This sample shows how to write a simple unit test for a function,
    // using Google C++ testing framework.
    //
    // Writing a unit test using Google C++ testing framework is easy as 1-2-3:
    
    
    // Step 1. Include necessary header files such that the stuff your
    // test logic needs is declared.
    //
    // Don't forget gtest.h, which declares the testing framework.

    #include &lt;limits.h&gt;
    #include "sample1.h"
    #include "gtest/gtest.h"


    // Step 2. Use the TEST macro to define your tests.
    //
    // TEST has two parameters: the test case name and the test name.
    // After using the macro, you should define your test logic between a
    // pair of braces.  You can use a bunch of macros to indicate the
    // success or failure of a test.  EXPECT_TRUE and EXPECT_EQ are
    // examples of such macros.  For a complete list, see gtest.h.
    //
    // <TechnicalDetails>
    //
    // In Google Test, tests are grouped into test cases.  This is how we
    // keep test code organized.  You should put logically related tests
    // into the same test case.
    //
    // The test case name and the test name should both be valid C++
    // identifiers.  And you should not use underscore (_) in the names.
    //
    // Google Test guarantees that each test you define is run exactly
    // once, but it makes no guarantee on the order the tests are
    // executed.  Therefore, you should write your tests in such a way
    // that their results don't depend on their order.
    //
    // </TechnicalDetails>
    
    
    // Tests Factorial().

    // Tests factorial of negative numbers.
    TEST(FactorialTest, Negative) {
      // This test is named "Negative", and belongs to the "FactorialTest"
      // test case.
      EXPECT_EQ(1, Factorial(-5));
      EXPECT_EQ(1, Factorial(-1));
      EXPECT_GT(Factorial(-10), 0);

      // <TechnicalDetails>
      //
      // EXPECT_EQ(expected, actual) is the same as
      //
      //   EXPECT_TRUE((expected) == (actual))
      //
      // except that it will print both the expected value and the actual
      // value when the assertion fails.  This is very helpful for
      // debugging.  Therefore in this case EXPECT_EQ is preferred.
      //
      // On the other hand, EXPECT_TRUE accepts any Boolean expression,
      // and is thus more general.
      //
      // </TechnicalDetails>
    }
    
    // Tests factorial of 0.
    TEST(FactorialTest, Zero) {
      EXPECT_EQ(1, Factorial(0));
    }
    
    // Tests factorial of positive numbers.
    TEST(FactorialTest, Positive) {
      EXPECT_EQ(1, Factorial(1));
      EXPECT_EQ(2, Factorial(2));
      EXPECT_EQ(6, Factorial(3));
      EXPECT_EQ(40320, Factorial(8));
    }
    
    
    // Tests IsPrime()
    
    // Tests negative input.
    TEST(IsPrimeTest, Negative) {
      // This test belongs to the IsPrimeTest test case.
    
      EXPECT_FALSE(IsPrime(-1));
      EXPECT_FALSE(IsPrime(-2));
      EXPECT_FALSE(IsPrime(INT_MIN));
    }
    
    // Tests some trivial cases.
    TEST(IsPrimeTest, Trivial) {
      EXPECT_FALSE(IsPrime(0));
      EXPECT_FALSE(IsPrime(1));
      EXPECT_TRUE(IsPrime(2));
      EXPECT_TRUE(IsPrime(3));
    }
    
    // Tests positive input.
    TEST(IsPrimeTest, Positive) {
      EXPECT_FALSE(IsPrime(4));
      EXPECT_TRUE(IsPrime(5));
      EXPECT_FALSE(IsPrime(6));
      EXPECT_TRUE(IsPrime(23));
    }


## 扩展阅读

* [轻松编写 C++ 单元测试 介绍全新单元测试框架组合： googletest 与 googlemock](http://www.ibm.com/developerworks/cn/linux/l-cn-cppunittest/index.html)
* [玩转Google开源C++单元测试框架Google Test系列(gtest)(总)](http://www.cnblogs.com/coderzh/archive/2009/04/06/1426755.html)


