---
layout: post
title: C/C++单元测试工具
date: 2013-03-23
categories:
  - 技术
tags:
  - C/C++
  - 单元测试
---
## 争论不休

![](/img/article/2013-03/23-01.png)

写代码的时候，要不要进行单元测试？多细的粒度合适？.......这是一个争论不休的问题。看这里[酷壳： 单元测试要做多细？](http://coolshell.cn/articles/8209.html)，敏捷开发实践方法的奠基人，XP和TDD的创造者Kent Beck的看法：
    “老板为我的代码付报酬，而不是测试，所以，我对此的价值观是——测试越少越好，少到你对你的代码质量达到了某种自信（我觉得这种的自信标准应该要高于业内的标准，当然，这种自信也可能是种自大）。如果我的编码生涯中不会犯这种典型的错误（如：在构造函数中设了个错误的值），那我就不会测试它。我倾向于去对那些有意义的错误做测试，所以，我对一些比较复杂的条件逻辑会异常地小心。当在一个团队中，我会非常小心的测试那些会让团队容易出错的代码。”

可是，再看看[List of unit testing frameworks](http://en.wikipedia.org/wiki/List_of_unit_testing_frameworks)，你就可以想象多少`程序员`在写TDD了。针对C/C++的测试工具，就那么多，可见一斑。

争论止于生活。`如果软件没有测试集，别人一看就没有使用的信心了，开源软件尤其如此`。看看Redis、Nginx、Sqlite、MySQL以及PostgreSQL等开源软件的测试集，就明了了。如果你是C/C++程序员，也想搞单元测试，可以选择[CppUnit - C++ port of JUnit](http://sourceforge.net/projects/cppunit/)、[Boost Test](http://www.boost.org/)及[Google Test](http://code.google.com/p/googletest/)等等。

这里先整理一些材料，后期针对Google Test进行一些总结和分享。


## 扩展阅读

* [酷壳： 单元测试要做多细？](http://coolshell.cn/articles/8209.html)
* [轻松编写 C++ 单元测试 介绍全新单元测试框架组合： googletest 与 googlemock](http://www.ibm.com/developerworks/cn/linux/l-cn-cppunittest/index.html)
* [玩转Google开源C++单元测试框架Google Test系列(gtest)(总)](http://www.cnblogs.com/coderzh/archive/2009/04/06/1426755.html)
* [便利的开发工具: CppUnit 快速使用指南](http://www.ibm.com/developerworks/cn/linux/l-cppunit/index.html)
* [Testing Framework](http://c2.com/cgi/wiki?TestingFramework)
* [CppUnit源码解读](http://www.360doc.com/content/12/1117/15/9200790_248397237.shtml)
* [CppUnit CookBook 中文版](http://download.51testing.com/ddimg/uploadsoft/20100414/CppUnitCookBook.pdf)
* [开放源码 C/C++ 单元测试工具，第 1 部分: 了解 Boost 单元测试框架](http://www.ibm.com/developerworks/cn/aix/library/au-ctools1_boost/)
* [开放源码 C/C++ 单元测试工具，第 2 部分: 了解 CppUnit](http://www.oschina.net/question/5189_7704)
* [开放源码 C/C++ 单元测试工具，第 3 部分: 了解 CppTest](http://www.oschina.net/question/12_8264)
* [用google mock模拟C++对象](http://www.uml.org.cn/c++/201203313.asp)
* [单元测试的七种境界](http://www.yeeyan.org/articles/view/zhaorui/39868)
* [InfoQ: TDD推荐教程](http://www.infoq.com/cn/news/2009/05/recommended-tdd-tutorials)
* [C单元测试包设计与实现](http://tonybai.com/2005/11/08/the-design-and-implementation-of-c-unittest-framework/)
* [单元测试准则](https://github.com/brantyoung/zh-unit-testing-guidelines)
* [List of unit testing frameworks](http://en.wikipedia.org/wiki/List_of_unit_testing_frameworks)
* [Evil Unit Tests](http://www.javaranch.com/journal/200603/EvilUnitTests.html)


## 开源项目

* CppUnit - C++ port of JUnit [http://sourceforge.net/projects/cppunit/](http://sourceforge.net/projects/cppunit/)
* Boost [http://www.boost.org/](http://www.boost.org/)
* Lcut, a Lightweight C Unit Testing framework [http://code.google.com/p/lcut/](http://code.google.com/p/lcut/)
* Google Test [http://code.google.com/p/googletest/](http://code.google.com/p/googletest/)
* GoogleMock，Google C++ Mocking Framework [http://code.google.com/p/googlemock/](http://code.google.com/p/googlemock/)


