---
layout: post
title: 通过expect实现sftp等自动化交互
date: 2013-04-14
categories:
  - 技术
tags:
  - expect
---
## 自动化交互

如果不配置基于ssh的无密钥自动交互，还有什么方式可是实现telnet、ftp及sftp等操作流程的自动交互呢？

使用[expect](http://sourceforge.net/projects/expect/)编写脚本，是其中的一种选择（当然，需要客户端主机安装有`expect`）。

    Expect, an extension to the Tcl scripting language written by Don Libes, is a program to automate interactions with programs that expose a text terminal interface. Expect is available for Unix, Microsoft Windows, and other systems. It is used to automate control of interactive applications such as telnet, ftp, passwd, fsck, rlogin, tip, ssh, and others. Expect uses pseudo terminals (Unix) or emulates a console (Windows), starts the target program, and then communicates with it, just as a human would, via the terminal or console interface. Tk, another Tcl extension, can be used to provide a GUI.

## 具体示例

### 自动化ftp交互实现

<pre class="prettyprint linenums">
#!/usr/bin/expect

# 远程主机信息配置
set ftp_host          192.168.1.11
set ftp_port          21
set ftp_user          zhangsan
set ftp_password      zhangsan123
set ftp_remote_dir    /home/zhangsan/sql

# 本地信息配置
set local_dir         /home/lisi/sqllog
set file_pattern      *.sql
set timeout           -1

# ftp get files, then mdelete the original files
spawn ftp $ftp_host $ftp_port
expect "Name*"
send "$ftp_user\r"
expect "Password:"
send "$ftp_password\r"
expect "ftp>"
send "cd $ftp_remote_dir\r"
expect "ftp>" 
send "lcd $local_dir\r"
expect "ftp>" 
send "prompt\r"
expect "ftp>" 
send "binary\r"
expect "ftp>"
send "mget $file_pattern\r"
expect "ftp>"
send "mdelete $file_pattern\r"
expect "ftp>" 
send "quit\r"
expect eof
</pre>

### 自动化sftp交互实现

<pre class="prettyprint linenums">
#!/usr/bin/expect

# 远程主机信息配置
set sftp_host           192.168.1.12
set sftp_port           22
set sftp_user           wangwu
set sftp_password       wang123
set sftp_remote_dir     /home/wangwu/sqllog

# 本地信息配置
set local_dir           /home/lisi/sqllog
set local_bak_dir       /home/lisi/sqllog_bak
set file_pattern        *
set timeout             -1

#sftp put files to remote server
spawn sftp -oPort=$sftp_port $sftp_user@$sftp_host
expect "*assword*"
send "$sftp_password\r"
expect "sftp>"
send "lcd $local_dir\r"
expect "sftp>"
send "cd $sftp_remote_dir\r"
expect "sftp>"
send "mput $file_pattern\r"
expect "sftp>"
send "! mv -f $local_dir/$file_pattern $local_bak_dir\n"
expect "sftp>"
send "quit\r"
expect eof
</pre>


## 扩展阅读

* [http://en.wikipedia.org/wiki/Expect](http://en.wikipedia.org/wiki/Expect)
* [Advanced Programming in Expect: A Bulletproof Interface](http://www.cotse.com/dlf/man/expect/bulletproof1.htm)
* [Expect man page](http://linux.die.net/man/1/expect)


## 祝大家玩的开心

