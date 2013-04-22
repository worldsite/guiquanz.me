---
layout: post
title: nicstat，网络流量统计利器
date: 2013-04-20
categories:
  - 技术
tags:
  - nicstat
---
## nicstat简介

[nicstat](https://blogs.oracle.com/timc/entry/nicstat_the_solaris_and_linux)是，一块非常好用的网络流量统计工具，比nestat舒服很多。nicstat是一块源于Solaris的工具，后来被Tim Cook移植到了Linux平台。

    `nicstat` is to network interfaces as `iostat` is to disks, or `prstat` is to processes.

`nicstat`最吸引人的特性，如下：

    Reports bytes in & out as well as packets.
    Normalizes these values to per-second rates.
    Reports on all interfaces (while iterating)
    Reports Utilization (rough calculation as of now)
    Reports Saturation (also rough)
    Prefixes statistics with the current time


## 软件部署及使用

### 安装nicstat

`nicstat`，目前是1.92版本。可以从[http://sourceforge.net/projects/nicstat/files/](http://sourceforge.net/projects/nicstat/files/)下载源代码文件，解压之后，进行安装。如果在64位平台安装，涉及对Makefile.Linux的修改，具体就是去掉`-m32`限制。流程，如下：
     
    i@home nicstat-src-1.92> diff Makefile.Linux64 Makefile.Linux
    17c17
     < CFLAGS =	$(COPT) 
     ---
     > CFLAGS =	$(COPT) -m32 
      
    i@home nicstat-src-1.92> make -f Makefile.Linux64
     
### 工具使用

（1）查看网卡速度
           
    i@home nicstat-src-1.92> ./nicstat.sh -l
    Int      Loopback   Mbit/s Duplex State
    vmnet8         No        0   unkn    up
    lo            Yes        -   unkn    up
    wlan0          No        0   unkn    up
    vmnet1         No        0   unkn    up
    
（2）查看tcp链接外联和内联的个数，重置，Drops信息还有包重传率等信息
     
    i@home nicstat-src-1.92> ./nicstat.sh -t
    15:02:03    InKB   OutKB   InSeg  OutSeg Reset  AttF %ReTX InConn OutCon Drops
    TCP         0.00    0.00    7.91    3.46  0.05  0.03 0.000   0.00   0.21  0.00

（3）以M为单位，显示统计结果
    
    i@home nicstat-src-1.92> ./nicstat.sh -m
        Time      Int   rMbps   wMbps   rPk/s   wPk/s    rAvs    wAvs %Util    Sat
    14:34:02   vmnet8    0.00    0.00    0.00    0.00    0.00    0.00  0.00   0.00
    14:34:02       lo    0.00    0.00    0.07    0.07   412.8   412.8  0.00   0.00
    14:34:02    wlan0    0.08    0.01    9.93    4.03  1085.7   181.7  0.00   0.00
    14:34:02   vmnet1    0.00    0.00    0.00    0.00    0.00    0.00  0.00   0.00
     
**注意**： 网卡的Util（利用率）以及Saturation（This the number of errors/second seen for the interface）在实践中用途挺大的。

（4）以5秒为间隔，采集数据
    
    i@home nicstat-src-1.92> ./nicstat.sh 5
        Time      Int   rKB/s   wKB/s   rPk/s   wPk/s    rAvs    wAvs %Util    Sat
    14:34:07   vmnet8    0.00    0.00    0.00    0.00    0.00    0.00  0.00   0.00
    14:34:07       lo    0.03    0.03    0.07    0.07   412.8   412.8  0.00   0.00
    14:34:07    wlan0   10.53    0.72    9.93    4.03  1085.7   181.7  0.00   0.00
    14:34:07   vmnet1    0.00    0.00    0.00    0.00    0.00    0.00  0.00   0.00
        Time      Int   rKB/s   wKB/s   rPk/s   wPk/s    rAvs    wAvs %Util    Sat
    14:34:12   vmnet8    0.00    0.00    0.00    0.00    0.00    0.00  0.00   0.00
    14:34:12       lo    0.00    0.00    0.00    0.00    0.00    0.00  0.00   0.00
    14:34:12    wlan0    0.70    0.00    2.00    0.00   357.2    0.00  0.00   0.00
    14:34:12   vmnet1    0.00    0.00    0.00    0.00    0.00    0.00  0.00   0.00
     

## 扩展阅读

* [nicstat主页](https://blogs.oracle.com/timc/entry/nicstat_the_solaris_and_linux)


## 祝大家玩的开心


