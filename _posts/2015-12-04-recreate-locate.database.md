---
layout: post
title: 重建 locate.database 文件
date: 2015-12-04
categories:
  - 技术
tags:
  - locate.database
---
## locate

`locate` 是快速定位文件的好工具，爱不释手（zhuang B），不过需要定时更新数据库。由于手贱将`/var/db/locate.database`文件删除了，导致如下的错误：

```shell
$ locate -0Scims

locate: mmap(2) not implemented

WARNING: The locate database (/var/db/locate.database) does not exist.
To create the database, run the following command:

  sudo launchctl load -w /System/Library/LaunchDaemons/com.apple.locate.plist

Please be aware that the database can take some time to generate; once
the database has been created, this message will no longer appear.

```

当然，如果你按说明执行 `launchctl` 命令行，`/var/db/locate.database` 文件是不会被重建的(我这里是这样)。那该怎么操作呢？其实，执行更新命令 `/usr/libexec/locate.updatedb` 即可。具体操作，如下：(命令执行有点`慢`，可以去喝杯 `coffee`)

```shell

$ sudo /usr/libexec/locate.updatedb

```

Ok，locate 又荣归了。


## 扩展阅读


## 祝大家玩的开心

## 编程之道，就在[编程之美]

![编程之美](/img/weixin_qr.jpg)

