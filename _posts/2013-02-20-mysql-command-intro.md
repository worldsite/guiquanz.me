几个人--- 
layout: post
title: MySQL基础命令操作
date: 2013-02-20
categories:
  - 技术
tags:
  - MySQL
---
## 服务管理

启动`MySQL`服务器： sudo service mysqld start

终止服务： sudo service mysqld stop


## 账户管理

创建 root 账户密码（默认密码为空）： mysqladmin -u root -password $%\_efg_234_

修改 root 账户密码： mysqladmin -u root -p $%\_efg_234_ -password _xyz_&*!

通过执行SQL修改密码（有权限要求）：

    mysql> UPDATE MySQL.user 
           SET password=PASSWORD('$%_efg_234_') WHERE User=’root’;
     
    mysql> FLUSH PRIVILEGES;


... `待续`


