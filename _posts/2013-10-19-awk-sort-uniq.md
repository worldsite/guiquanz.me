---
layout: post
title: 巧妙使用awk、sort和uniq
date: 2013-10-19
categories:
  - 技术
tags:
  - awk
  - sort
  - uniq

---
## 组合之妙

`Unix`，就是一个很精巧的工具箱。只要简单的把各种工具组合一下，就可以达成很多有趣的事。现在我们搭配一下awk、sort和uniq，看看能玩点什么？

### 准备数据文件

本文使用到的数据文件（test）内容（行内分隔为'\t'。文件末尾为一个空行。），如下：
<pre class="prettyprint">
i@home> cat test
aa	dd
bb
cc	xx
dd
aa
bb
cc
ee
rr
ttttt

</pre>
      
### 取出第1列，升序排列，显示去重复后的结果

<pre class="prettyprint">
i@home> cat test |awk -F '\t' '{ print $1 }' |sort |uniq

aa
bb
cc
dd
ee
rr
ttttt
</pre>

### 取出第1列，升序排列，显示重复出现的行

<pre class="prettyprint">
i@home> cat test |awk -F '\t' '{ print $1 }' |sort |uniq -d
aa
bb
cc
</pre>

### 取出第1列，升序排列，显示仅出现一次的行

<pre class="prettyprint">
i@home> cat test |awk -F '\t' '{ print $1 }' |sort |uniq -u

dd
ee
rr
ttttt
</pre>

### 取出第1列，升序排列，统计每行的次数

<pre class="prettyprint">
i@home> cat test |awk -F '\t' '{ print $1 }' |sort |uniq -c
   1 
   2 aa
   2 bb
   2 cc
   1 dd
   1 ee
   1 rr
   1 ttttt
</pre>

这里只是一个简单的功能演示，可以用这个组合工具去执行`线上系统日志分类统计`，`独立IP访问次数统计`等等。Open Your Mind! 大胆尝试。


## 扩展阅读

* `酷壳`，[AWK 简明教程](http://coolshell.cn/articles/9070.html)


## 祝大家玩的开心


## 编程之道，就在[编程之美]

![编程之美](/img/weixin_qr.jpg)


