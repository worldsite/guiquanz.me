---
layout: post
title: iOS 模拟器中文输入问题解决
date: 2015-10-30
categories:
  - 技术
tags:
  - 中文问题
---
## iOS 中文问题


## 模拟器不弹出虚拟键盘

 模拟器菜单 -> Hardware -> Keyboard, 然后点击 `Toggle Software Keyboard`, 重启模拟器。


## 虚拟键盘没有中文输入法


### 修改 Scheme 配置

Xcode 菜单项 -> Product -> Scheme -> "Edit Scheme"。然后，在弹出的界面选择 Options 项， 设置 Application Region 为 “中国”。


### 设置软件本地化

在 Xcode 中选择 target -> info -> locolization 添加 "简体中文"。


## 扩展阅读


## 祝大家玩的开心

## 编程之道，就在[编程之美]

![编程之美](/img/weixin_qr.jpg)

