---
layout: post
title: C++构造函数机制
date: 2013-04-13
categories:
  - 技术
tags:
  - C++构造函数
---
## 这是一个很简单的问题程序，检测一下你对C++构造函数机制的理解

     1	 
     2	    class A {
     3	     public:
     4	       ~A(){};
     5	    };
     6	  
     7	    class B {
     8	     public:
     9	       B(){};
    10	       ~B(){};
    11	    };
    12	 
    13	    class C {
    14	     public:
    15	       ~C(){};
    16	
    17	     private:
    18	       C(){};
    19	    };
    20	 
    21	    class D {
    22	     public:
    23	       D(int a):a_(a){};
    24	       ~D(){};
    25	  
    26	     private:
    27	       int a_;
    28	    };
    29	   
    31	    class E {
    32	     public:
    33	       explicit E(int a):a_(a){};
    34	       ~E(){};
    35	  
    36	     private:
    37	       int a_;
    38	    };
    39	  
    40	    int main(void) {
    41	      A a;
    42	      B b;
    43	      C c;
    44	      D d;
    45	      E e1 (2);
    46	      E e2;
    47	  
    48	      return 0;
    49	    }
    50	


这个程序能正常编译吗？哪里有问题？为什么？



