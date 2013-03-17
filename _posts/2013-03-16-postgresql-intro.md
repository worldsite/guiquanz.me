---
layout: post
title: 一种不错的PostgreSQL应用解决方案
date: 2013-03-16
categories:
  - 技术
tags:
  - PgBouncer
  - Slony-I
  - PostgreSQL
---
## PostgreSQL

![PostgreSQL](/img/article/2013-03/16-01.png)

[PgBouncer](http://wiki.postgresql.org/wiki/PgBouncer) + [Slony-I](http://slony.info/) + [PostgreSQL](http://www.postgresql.org/)是一组不错的数据库应用解决方案。

`PostgreSQL` is a powerful, open source object-relational database system. It has more than 15 years of active development and a proven architecture that has earned it a strong reputation for reliability, data integrity, and correctness. It runs on all major operating systems, including Linux, UNIX (AIX, BSD, HP-UX, SGI IRIX, Mac OS X, Solaris, Tru64), and Windows. It is fully ACID compliant, has full support for foreign keys, joins, views, triggers, and stored procedures (in multiple languages). It includes most SQL:2008 data types, including INTEGER, NUMERIC, BOOLEAN, CHAR, VARCHAR, DATE, INTERVAL, and TIMESTAMP. It also supports storage of binary large objects, including pictures, sounds, or video. It has native programming interfaces for C/C++, Java, .Net, Perl, Python, Ruby, Tcl, ODBC, among others, and exceptional documentation.

`Slony-I` is a "master to multiple slaves" replication system for PostgreSQL supporting cascading (e.g. - a node can feed another node which feeds another node...) and failover.

![Slony-I](/img/article/2013-03/16-05.jpg)

`PgBouncer` is a lightweight connection pooler for PostgreSQL.

![PgBouncer](/img/article/2013-03/16-07.jpg)


## 扩展阅读

* [也谈PostgreSQL的同步配置(Slony) ](http://www.laruence.com/2009/06/09/912.html)
* [PGBouncer介绍及使用方式](http://blog.csdn.net/barfoo/article/details/3351499)
* [PostgreSQL连接池pgbouncer的使用](http://my.oschina.net/Kenyon/blog/73935)
* [PostgreSQL 9.0 Official Documentation Books](http://www.linbrary.com/postgresql/900/index.html)
* Volume I   The SQL Language
* Volume II  Server Administration
* Volume III Server Programming
* Volume IV  Reference
* Volume V   Internals and Appendix


## 源代码

* `PgBouncer` [https://github.com/markokr/pgbouncer-dev](https://github.com/markokr/pgbouncer-dev)
* `Slony-I` [git://git.postgresql.org/git/slony1-engine.git](git://git.postgresql.org/git/slony1-engine.git)
* `Postgres` [https://github.com/postgres/postgres](https://github.com/postgres/postgres)


