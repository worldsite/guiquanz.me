---
layout: post
title: 如何阻止 Mac 进入休眠状态
date: 2016-01-25
categories:
  - 技术
tags:
  - 技术
---
## 阻止 Mac 进入休眠状态

有时想短暂的离开 Mac 又不想其进入休眠状态，该如何设置？

1、毒药正解(执行命令)：`pmset noidle`

后悔药：`Ctrl + C` 取消命令执行 或 关闭终端。


pmset   # OS X 提供的命令行管理电源的工具，其功能远不止于此。

pmset -g   # 查看当前电源的使用方案

sudo pmset -b displaysleep 10  # 设置电池供电时，显示器 10 分钟内进入睡眠

sudo pmset schedule wake "01/01/16 19:00:00"  # 设置电脑在 2016年1月1日晚7点唤醒电脑


2、使用 caffeinate 命令

在终端输入以下命令：

caffeinate -u -t 14400

其中的数字可以输入任意值，代表你想让系统不进入休眠的时长。


取消操作：在终端中输入 caffeinate，后按下 Control + C 退出。


## 扩展阅读



## 祝大家玩的开心

## 编程之道，就在[编程之美]

![编程之美](/img/weixin_qr.jpg)

