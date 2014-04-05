---
layout: post
title: 15分钟玩转PostgreSQL
date: 2013-08-01
categories:
  - 技术
tags:
  - PostgreSQL
---
[![PostgreSQL](/img/article/2013-03/16-01.png)](http://www.postgresql.org)

## 安装postgresql

```bash

    mkdir -p ~/postgresql && cd ~/postgresql
```

### 下载 9.2.4版本的源代码

```bash

    wget http://ftp.postgresql.org/pub/source/v9.2.4/postgresql-9.2.4.tar.gz 
    tar xfvz postgresql-9.2.4.tar.gz
    cd postgresql-9.2.4
```

### 使用默认安装配置。可以执行`./configure --help`查看配置参数，如`--prefix`等

```bash

    ./configure --prefix=/usr/local/pgsql \
                --mandir=/usr/local/pgsql/man \
                --docdir=/usr/local/pgsql/doc
    sudo make && make install
```

### 配置环境变量

```bash

    vim ~/.bash_profile
     ## 追加pgsql相关路径，默认为`/usr/local/pgsql`，然后保存、退出
    export PGSQL_HOME=/usr/local/pgsql
    export PATH=$PGSQL_HOME/bin:$PATH
     ## 执行配置文件，让配置生效
    . ~/.bash_profile
```

### 试运行

```bash

    pg_config --version
```

### 编译后生成的可执行文件

```bash

    tree /usr/local/pgsql/bin
    /usr/local/pgsql/bin
    |- clusterdb
    |- createdb
    |- createlang
    |- createuser
    |- dropdb
    |- droplang
    |- dropuser
    |- ecpg
    |- initdb
    |- pg_basebackup
    |- pg_config
    |- pg_controldata
    |- pg_ctl
    |- pg_dump
    |- pg_dumpall
    |- pg_receivexlog
    |- pg_resetxlog
    |- pg_restore
    |- postgres
    |- postmaster -> postgres
    |- psql
    |- reindexdb
    `-- vacuumdb
```

### 配置动态库路径：编辑配置文件，进行客户端c开发时，需要这些动态库

```bash

    sudo vim /etc/ld.so.conf
     # 在配置文件中追加如下内容。保存、退出
    /usr/local/pgsql/lib
```

### 更新配置

```bash

    sudo ldconfig
```

### 创建主机用户，专门用于运行postgres

```bash

    sudo useradd postgres
    sudo passwd postgres
```

### 创建数据库目录

```bash

    sudo mkdir -p /usr/local/pgsql/data
    sudo chown postgres /usr/local/pgsql/data
```

### 初始化数据库

```bash

    su - postgres
    /usr/local/pgsql/bin/initdb -D /usr/local/pgsql/data
     
Success. You can now start the database server using:

    /usr/local/pgsql/bin/postgres -D /usr/local/pgsql/data
or
    /usr/local/pgsql/bin/pg_ctl -D /usr/local/pgsql/data -l logfile start
```

### 配置网络链接

```bash

    /usr/local/pgsql/pg_hba.conf
    host  bpsimple  neil  192.168.0.3/32  md5
    
    /usr/local/pgsql/postgresql.conf
   listen_addresses='localhost'
```

### 启动服务器

```bash 

   /usr/local/pgsql/bin/postmaster -i -D /usr/local/pgsql/data >logfile 2>&1 &
```

便捷操作命令：

```bash

    pg_ctl start -D /usr/local/pgsql/data
    pg_ctl stop -D /usr/local/pgsql/data
    pg_ctl restart -D /usr/local/pgsql/data
```

### 链接服务器

```bash

    /usr/local/pgsql/bin/psql  #默认链接本地的，当前账户同名的数据库
    /usr/local/pgsql/bin/psql -d template1 #样版库
```

## 扩展阅读


## 祝大家玩的开心

