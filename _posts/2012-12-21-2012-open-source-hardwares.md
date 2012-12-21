--- 
layout: post
title: 2012最火爆的开源硬件产品
date: 2012-12-21
categories:
  - 技术
tags:
  - Raspberry Pi
---

## 1、Raspberry Pi

![](/img/article/raspberrypi/raspberrypi.jpg)

Raspberry Pi，是一款基于Linux系统的个人电脑，配备一枚700MHz的处理器，256Mb内存，支持SD卡和Ethernet，拥有两个USB接口，以及 HDMI和RCA输出支持。有消息称，虽然Raspberry Pi看起来非常的迷你——只有一张信用卡大小，但是它能够运行像《雷神之锤三：竞技场》这样的游戏和进行1080p视频的播放。和最终售价 200 美元的 OLPC 不一样，体积大概是一个火柴盒大小，硬件基础是 ARM，操作系统采用开源的Linux系统，比如 Debian、ArchLinux，自带的 Iceweasel、KOffice 等软件能够满足基本的网络浏览，文字处理以及计算机学习的需要，分A,B两中型号，其中A型售价仅25美元。

### Pi软件市场

Raspberry Pi红了，相关的软件市场也来了。[http://store.raspberrypi.com/](http://store.raspberrypi.com/).

![](/img/article/raspberrypi/pi-store.png)


### Pi相关开源软件

1、[官方Raspberry Pi开源视频驱动源代码](https://github.com/raspberrypi/userland)；

Raspberry Pi基金会宣布在BSD许可证下开源其视频驱动源代码。新的Userland库代码托管在GitHub上。Raspberry Pi基金会表示，Userland库开源能够帮助那些想要在Raspberry Pi移植或者想使用其它操作系统进行替代更便捷。

2、剑桥大学计算机实验室的[Baking Pi - Operating Systems Development](http://www.cl.cam.ac.uk/freshers/raspberrypi/tutorials/os/?test=true);

3、Raspberry Pi平台的开源音乐播放器[pyscmpd](https://github.com/wendlers/pyscmpd)。

pyscmpd是一个音乐播放器守护程序，主要用于Raspberry Pi平台上。实现了MPD协议的子集，可连接到SoundCloud.com然后直接播放音乐流。可通过 MPD 支持大量前端的客户端，包括ncmpcpp和Sonata.

![](/img/article/raspberrypi/pyscmpd.png)


## 2、开源3D打印机——RepRap

![](/img/article/raspberrypi/reprap.jpg)

[RepRap](reprap.org)是一种能够打印出塑料实物（以塑料为原材料）的开源桌上3D打印机。由于RepRap可以打印出大部分的自身（塑料）部件, 且RepRap是一个任何人都可以花费自己的时间和精力制作出的玩意——传说中的自我复制机。这就意味着当你自己弄出一个来后，你除了可以用它没事打点有用的玩意，你还可以没事再复制出一个送给你的朋友。

[Reprap.org](http://reprap.org/wiki/Main_Page)是一个wiki社区网站, 意味这你可以编辑大部分网站上的内容, 或还可以, 创建你自己编辑的新内容……


## 3、Arduino依旧火红

[Arduino](http://www.arduino.cc/)，是一个基于开放原始码的软硬体平台，构建于开放原始码simple I/O介面版，并且具有使用类似Java，C语言的Processing/Wiring开发环境。

![](/img/article/raspberrypi/arduino.png)

让您可以快速使用Arduino语言与Macromedia Flash, Processing, Max/MSP, Pure Data, SuperCollider…等软体，作出互动作品。 Arduino可以使用开发完成的电子元件例如Switch或感测器或其他控制器件、LED、步进马达或其他输出装置。 Arduino也可以独立运作成为一个可以跟软体沟通的介面，例如说： Macromedia Flash, Processing, Max/MSP, Pure Data, VVVV或其他互动软体…。 Arduino开发ＩＤＥ介面基于开放原始码原，可以让您免费下载使用开发出更多令人惊艳的互动作品。

     *基于创用CC开放原始码的电路图设计， 
     *基于创用CC开放原始码的程式开发环境 
     *免费下载，也可依需求自己修改!!遵照姓名标示。您必须按照作者或授权人所指定的方式，表彰其姓名； 
     *依相同方式分享，若您改变、转变著作，当散布该衍生著作时，您需采用与本著作相同或类似的授权条款。 
     * Arduino可使用ICSP线上烧入器，将「bootloader」烧入新的IC晶片。 
     *可依据官方电路图，简化Arduino模组，完成独立运作的微处理控制。 
     *可简单地与感测器，各式各样的电子元件连接（EX：红外线，超音波，热敏电阻，光敏电阻，伺服马达，…等） 
     *支援多样的互动程式ex: Macromedia Flash、Max/Msp、VVVV、PD、C、Processing、、、等 
     *使用低价格的微处理控制器(ATMEGA 8-168) NT$120~NT$150 
     * USB介面，不需外接电源。另外有提供9VDC输入 
     *应用方面，利用Arduino，突破以往只能使用滑鼠，键盘，CCD等输入的装置的互动内容，可以更简单地达成单人或多人游戏互动。

![](/img/article/raspberrypi/arduino-ide.png)


### 图形化的Arduino开发环境——ArduBlock

[ArduBlock](http://blog.ardublock.com/),是一个开源的图形化的Arduino开发环境，针对的是程序入门的小朋友来学习Arduino这样物理运算应用的开发。

![](/img/article/raspberrypi/ardublock.jpg)

