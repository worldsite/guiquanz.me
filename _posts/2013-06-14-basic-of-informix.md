---
layout: post
title: 如何查看及修改Informix库中表的锁级别
date: 2013-06-14
categories:
  - 技术
tags:
  - 锁级别
---

## 查看表的锁级别

* oncheck -pt database\_name:table\_name (需要特权账户)
* select tabname, locklevel from systables where tabname='table\_name'

## 设置锁级别

* alter table table\_name lock mode(row)

创建后可用以下命令导出来核对：

* dbschema -d dbname -t table\_name -ss file\_name

## 默认锁级别

默认locklevel是通过环境变量IFX\_DEF\_TABLE\_LOCKMODE可以设置。 例如: 

* export IFX\_DEF\_TABLE\_LOCKMODE=PAGE

## 扩展阅读


## 祝大家玩的开心

