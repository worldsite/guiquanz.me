---
layout: post
title: Google等域名屏蔽解决办法
date: 2014-05-11
categories:
  - 技术
tags:
  - 域名
---

经常会出现由于域名被屏蔽而无法访问相关网站的情况，如golang.org 就经常被墙掉。一般的解决办法是，查询主机对应的ip，然后修改本地主机的hosts配置。当然，如果ip都被屏蔽了，那就只有准备梯子翻墙了。

1、修改host配置的具体操作，如下：

(1)、访问http://ping.eu/ping地址，查询目标域名对应的ip

(2)、修改hosts配置

主机hosts的位置：

* Unix系统，在/etc/hosts

* Windows系统，在C:\WINDOWS\system32\drivers\etc\hosts

经查询，可以得到golang.org相关的ip如下：

173.194.75.141 golang.org 

173.194.75.141 play.golang.org

173.194.70.141 blog.golang.org

2、针对google等系统的IP地址

[smart hosts](https://github.com/smarthosts/smarthosts/blob/master/trunk/hosts),一个不错的域名与IP对应表项目。


3、针对google的翻墙镜像

* [针对google的翻墙镜像](https://github.com/greatfire/wiki)

Google 搜索：http://www.aol.com

Google 搜索：http://sinaapp.co

Google 搜索：https://s3-us-west-2.amazonaws.com/google2/index.html

Google 搜索：https://s3-us-west-1.amazonaws.com/google3/index.html

Google 搜索：https://s3-eu-west-1.amazonaws.com/google4/index.html

Google 搜索：https://s3-ap-northeast-1.amazonaws.com/google5/index.html

Google 搜索：https://s3-ap-southeast-2.amazonaws.com/google6/index.html

Google 搜索：https://s3-sa-east-1.amazonaws.com/google7/index.html

Google 搜索：https://s3-ap-southeast-1.amazonaws.com/google.cn/index.html

Google 搜索：https://s3.amazonaws.com/google./index.html

自由微博：https://s3-ap-southeast-1.amazonaws.com/freeweibo2/index.html

自由微博：https://s3.amazonaws.com/freeweibo./index.html

中国数字时代：https://s3-ap-southeast-1.amazonaws.com/cdtimes2/index.html

中国数字时代：https://s3.amazonaws.com/cdtimes./index.html

泡泡（未经审查的网络报道）：https://s3-ap-southeast-1.amazonaws.com/pao-pao2/index.html

泡泡（未经审查的网络报道）：https://s3.amazonaws.com/pao-pao/index.html

蓝灯(Lantern)以及自由微博和GreatFire.org官方中文论坛：https://lanternforum.greatfire.org


* [Google-IPs](https://github.com/justjavac/Google-IPs)


## 扩展阅读


## 祝大家玩的开心

## 编程之道，就在[编程之美]

![编程之美](/img/weixin_qr.jpg)

