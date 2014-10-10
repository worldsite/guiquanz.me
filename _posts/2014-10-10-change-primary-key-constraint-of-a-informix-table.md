---
layout: post
title: 修改Informix数据表primary key约束
date: 2014-10-10
categories:
  - 技术
tags:
  - informmix
---
## 场景描述

在INFORMIX数据库环境下，__向已存在的数据表增加字段并调整数据表primary key约束__的操作流程，如下：

##（1）向已有表增加字段

```text

SQL语法：alter table table_name add column_name column_type optional_restriction;

    如：alter table x_user add zip_code integer default 1000;
```

__注意点：如果新增加的字段需要增加到primary key约束中，并且原始数据表中已有数据，那么新增字段必须有默认值，否则操作会失败。__


##（2）查询已有表的主键约束名称

```text

SQL语法：select constrname from sysconstraints where constrtype='P' 
        and tabid=(select tabid from systables where tabname='table_name');
```

##（3）drop原始表的主键约束

```text

SQL语法：alter table table_name drop constraint 主键约束名; //（2）中查询出来的值
``` 

##（4）重新设置主键约束

```text

SQL语法：alter table table_name add constraint primary key(col_name1,col_name2,.....) [constrain 主键约束名];
```

__主键约束名可以用原始的名称，（2）中查询出来的值__


## 扩展阅读


## 祝大家玩的开心

## 编程之道，就在[编程之美]

![编程之美](/img/weixin_qr.jpg)

