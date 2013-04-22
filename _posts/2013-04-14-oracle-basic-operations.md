---
layout: post
title: Oracle数据库基础操作
date: 2013-04-14
categories:
  - 技术
tags:
  - Oracle
---
## 创建建库

<pre class="prettyprint linenums">
i@home> $ sqlplus "/as sysdba"
SQL> create user zhangsan identified by 'zhangsan';
SQL> grant dba to zhangsan;
</pre>


## 从现有的库导出表结构

假定现有一个库实例为“lisi/lisi@oracle_1”，使用exp工具从中导出所有表结构的操作，如下：
<pre class="prettyprint linenums">
i@home> mkdir -p ~/dbport && cd ~/dbport
i@home> exp lisi/lisi@oracle_1 file=lisi.dmp rows=n log=exp.log
</pre>

__如果，还需要导表的数据，只需要置rows=y即可__


## 将表结构导入到新建的库中

<pre class="prettyprint linenums">
i@home> cd ~/dbport
i@home> imp zhangsan/zhangsan@oracle_1 fromuser=lisi touser=zhangsan file=lisi.dmp log=imp.log
i@home> cd .. && rm -rf ~/dbport
</pre>


## 在库上执行指定的SQL文件

<pre class="prettyprint linenums">
i@home> sqlplus zhangsan/zhangsan@oracle_1
SQL> @ install.sql
或
SQL> start install.sql
</pre>

__如果，没有指定文件的绝对路径。默认路径为，当前操作目录。__


## 祝大家玩的开心

