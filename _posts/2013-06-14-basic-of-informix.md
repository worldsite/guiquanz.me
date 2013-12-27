---
layout: post
title: Informix特殊操作
date: 2013-06-14
categories:
  - 技术
tags:
  - Informix
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


## Informix SPL中TRACE示例

<pre class="prettyprint linenums">
  CREATE PROCEDURE items_pct(mac CHAR(3))
  DEFINE tp MONEY
  DEFINE mc_tot MONEY
  DEFINE pct DECIMAL

  --设置日志输出文件
  SET DEBUG FILE TO 'pathname'

  TRACE 'begin trace'

  -- 开始跟踪
  TRACE ON;

  LET tp = (SELECT SUM(total_price) FROM items)
  LET mc_tot = (SELECT SUM(total_price) FROM items
    WHERE manu_code = mac)
    LET pct = mc_tot / tp
    IF pct > .10 THEN
      RAISE EXCEPTION -745
    END IF

  -- 结束跟踪
  TRACE OFF

  END PROCEDURE
</pre>


## Informix导数据到Oracle范例
 
### 准备informix导出的数据文件：示例为t_register.unl 
 
### 准备控制文件：示例为 t_register.ctl, 内容如下：
<pre class="prettyprint linenums">
  LOAD DATA
  INFILE 't_register.unl'  --数据文件名
  INTO TABLE t_register  -- 目标表名
  REPLACE
  FIELDS TERMINATED BY '|'
  (  --目标表的字段名，顺序要和数据文件的字段顺序一致（*.unl）
    agentdn,
    agentclass,
    status,
    flag
  )
</pre>

### 导入数据

命令格式： 
     
  sqlldr userid=访问数据库实例的用户id control=控制文件  log=操作日志文件（如果不指定，打在终端上）。示例，如下：
 
* sqlldr userid=test/test control=t_register.ctl log=ctlog/test.log


## 扩展阅读


## 祝大家玩的开心

