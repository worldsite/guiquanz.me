---
layout: post
title: 解决 “pod update 时，读取 CocoaPods 版本信息出错“问题
date: 2015-07-11
categories:
  - 技术
tags:
  - iOS
---
## pod update 失败

[CocoaPods](http://www.cocoapods.org) 是 iOS 项目（Objectiv-C/C++ 等）依赖`集中化`管理工具（btw: `Swift`采用的是，去中心化的[Carthage](https://github.com/Carthage/Carthage)），整体效果不错，但这个用[Ruby](https://www.ruby-lang.org/zh_cn/)编写的工具，偶尔会蹦出诡异的错误。这次我就碰见`pod update`相关的错误。具体日志，如下：

```shell

[!] There was an error reading '/Users/xxxx/.cocoapods/repos/master/CocoaPods-version.yml'.
Please consult http://blog.cocoapods.org/Repairing-Our-Broken-Specs-Repository/ for more information.

```

从错误提示信息可知，读取`CocoaPods-version.yml`中的版本信息时出错了。


## 问题分析及解决

通过执行`pod update -v`之后，发现了一些蛛丝马迹：

```shell

[!] There was an error reading '/Users/xxxx/.cocoapods/repos/master/CocoaPods-version.yml'.
Please consult http://blog.cocoapods.org/Repairing-Our-Broken-Specs-Repository/ for more information.

/Library/Ruby/Gems/2.0.0/gems/cocoapods-0.35.0/lib/cocoapods/sources_manager.rb:316:in `rescue in version_information'
/Library/Ruby/Gems/2.0.0/gems/cocoapods-0.35.0/lib/cocoapods/sources_manager.rb:313:in `version_information'
/Library/Ruby/Gems/2.0.0/gems/cocoapods-0.35.0/lib/cocoapods/sources_manager.rb:234:in `check_version_information'
/Library/Ruby/Gems/2.0.0/gems/cocoapods-0.35.0/lib/cocoapods/command/repo.rb:57:in `block in run'
/Library/Ruby/Gems/2.0.0/gems/cocoapods-0.35.0/lib/cocoapods/user_interface.rb:49:in `section'
/Library/Ruby/Gems/2.0.0/gems/cocoapods-0.35.0/lib/cocoapods/command/repo.rb:49:in `run'
/Library/Ruby/Gems/2.0.0/gems/cocoapods-0.35.0/lib/cocoapods/command/setup.rb:84:in `add_master_repo'
/Library/Ruby/Gems/2.0.0/gems/cocoapods-0.35.0/lib/cocoapods/command/setup.rb:40:in `block in run'
/Library/Ruby/Gems/2.0.0/gems/cocoapods-0.35.0/lib/cocoapods/user_interface.rb:49:in `section'
/Library/Ruby/Gems/2.0.0/gems/cocoapods-0.35.0/lib/cocoapods/command/setup.rb:32:in `run'
/Library/Ruby/Gems/2.0.0/gems/claide-0.7.0/lib/claide/command.rb:271:in `run'
/Library/Ruby/Gems/2.0.0/gems/cocoapods-0.35.0/lib/cocoapods/command.rb:45:in `run'
/Library/Ruby/Gems/2.0.0/gems/cocoapods-0.35.0/bin/pod:43:in `<top (required)>'
/usr/bin/pod:23:in `load'
/usr/bin/pod:23:in `<main>'

```

原来`cocoapods`代码有执行错误，执行`sudo brew reinstall cocoapods`重新安装也没有解决问题。最后，参考[Pod setup error](https://github.com/CocoaPods/CocoaPods/issues/2908) 卸载`psych`之后，问题终于得以解决。

```shell

sudo gem uninstall psych
sudo gem uninstall cocoapods
sudo gem cleanup
sudo gem update
sudo gem instal cocoapods
pod update

```


## 扩展阅读

* [Pod setup error](https://github.com/CocoaPods/CocoaPods/issues/2908)


## 祝大家玩的开心

## 编程之道，就在[编程之美]

![编程之美](/img/weixin_qr.jpg)

