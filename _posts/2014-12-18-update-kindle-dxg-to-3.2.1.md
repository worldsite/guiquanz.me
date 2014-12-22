---
layout: post
title: Kindle DXG越狱、升级和汉化
date: 2014-12-18
categories:
  - 技术
tags:
  - Kindle
---
## 升级和汉化的好处

* __更好的web浏览器__。
* __汉化之后，可以完美的支持中文显示__。（如果安装多看，可用的系统版本很低，双系统不稳定而且很__耗电__，另外多看系统也很丑，不好用等）
* __加深__功能。对于灰度扫描的PDF文件，阅读体验改进明显。
* 其他尚待发现

## 升级过程

本文将介绍，如何将Kindle DXG原有2.5.8系统越狱升级到__3.2.1__版本,同时进行汉化处理。这是我的操作实录，仅供参考。__越狱有风险，请慎重考虑，后果自负。__

### 准备工作

相关文件下载(地址取自网络，不对其版权问题负责，请下载者慎重考虑) 3.2.1镜像文件

步骤1-3中涉及的文件，如下：

- tts-files.tar
- update_dxg-2.5.8-prepare_kindle.bin
- update_jailbreak_0.7.N_dxg_install.bin 
- update_kindle_3.2.1.bin

下载地址：http://dl.dbank.com/c0nw3a6bnd
下载地址：http://dl.vmall.com/c0iuax42ah

推荐__将Kinle DXG里面的书籍转移至别处，卸载一切汉化或者其他插件__。如果出错，可以查看readme文件，里面提供了出错提示的查询与解释。__DXG的系统版本为2.5.8，其他版本不保证成功__。

### 版本升级

0、将原有的文件，备份出去。如果安装了`多看`，请将其卸载掉。然后，__将Kindle DXG恢复为出厂配置__。

1、将Kindle DXG越狱 (__已越狱可跳过此步__) 连接电脑，把`update_jailbreak_0.7.N_dxg_install.bin`拷到kindle硬盘根目录下。
断开电脑连接，进入`menu->Settings->update your kindle`, Kindle开始越狱(需时约5分钟)，重启后重新连接电脑。

2、制作DXG 2.5.8系统的镜像 (备份自身的系统) 把`update_dxg-2.5.8-prepare_kindle.bin`拷到kindle硬盘根目录下。断开电脑连接，仍然是进入update your kindle, Kindle开始备份2.5.8的系统(需时约60分钟)，重启后重新连接电脑。
__将硬盘下output目录拷贝到它处妥善保存__。以下是我在备份时候的状态信息，可能有误差，仅供参考：

- 开始制作2.5.8系统备份镜像 
- VCreating image...
- Compressing image...
- Generating update package...
- GeneratiGenerating update...
- GeneratiGeneraCleaning up...
- GFlashing recovery kernel...
- 完成

3、升级Kindle 3.2.1系统把`update_kindle_3.2.1.bin`和`tts-files.tar`拷到kindle硬盘根目录下。断开电脑连接，进入`update your kindle`,Kindle开始升级至3.2.1，需时约35分钟。首次重启需时较长，大约需要20分钟左右。然后，在界面应该就已显示`3.2.1`了。

### 更新字体

执行完以上操作之后，部分中文还是不能正常显示，需要更新字体。具体操作，如下：

到[Snapshots of NiLuJe's hacks 2014-Dec-02](http://www.mobileread.com/forums/showthread.php?t=225030)，（建议使用最新版本），下载 [jailbreak-0.13.N-r11172.tar.xz](https://storage.sbg-1.runabove.io/v1/AUTH_38c8efe0a22243d0be9bf697b4d52111/mr-public/Legacy/kindle-jailbreak-0.13.N-r11172.tar.xz)和
[kindle-fonts-5.14.N-r11218.tar.xz](https://storage.sbg-1.runabove.io/v1/AUTH_38c8efe0a22243d0be9bf697b4d52111/mr-public/Legacy/kindle-fonts-5.14.N-r11218.tar.xz)其他的包，如果有需要。自行下载安装。__xz的文件，在Windows上可以用7zip解压__

（1）、越狱：解压`jailbreak-0.13.N-r11172.tar.xz`文件，取出Jailbreak下的`Update_jailbreak_0.13.N_dxg_install.bin`放到dxg的根目录，卸载USB，进入`update your kindle`，重启之后，重新连接PC；

（2）、安装字体：解压`kindle-fontpackager-1.0.N-r10686.tar.xz`文件，取出`Fonts`下的`Update_fonts_5.14.N_dxg_install.bin`和Fonts\src\下的`linkfonts`目录文件，放到dxg的根目录，卸载USB，进入`update your kindle，重启之后，中文正常显示了。深黑也能正常使用了。


## 安装Launchpad热键管理程序

Launchpad 是适用于 Kindle 的快捷键管理程序，就是以指定的键盘按键（或键盘组合）来快速运行特定程序或命令，如要体验一下快捷键，请马上按Ctrl+W。要在 Kindle 上安装 Launchpad，你可以执行以下步骤：

（1）、下载[lpad-pkg-001c.zip](http://www.mobileread.com/forums/attachment.php?attachmentid=65929&d=1296663715
)[或者是](http://www.mobileread.com/forums/showthread.php?t=97636)。

（2）、连接 Kindle 到电脑，将`lpad-pkg-001c.zip`解压包中的`update_launchpad_0.0.1c_k3w_install.bin`拷贝到 Kindle 根目录。进入`update your kindle`，重启之后，中文正常显示了。

__shift-shift-空格键（快速按键），然后屏幕左下角会出现success，表示安装成果。__


## 安装及使用ePub阅读器软件fbKindle

（1）、下载[fbKindle-bin.tar.gz](https://dev.mobileread.com/dist/h1uke/fbkindle/fbKindle-bin.tar.gz)文件
[或者](http://pan.baidu.com/s/1mg9Pu8w) __原始文件解压密码为：1__

（2）、将 Kindle 与电脑相连，并把下载的`fbKindle-bin.tar.gz`文件拷贝到 Kindle 根目录；

（3）、转到 Kindle 根下的 launchpad 目录，打开其中的`launchpad.ini`文件，并添加下列内容：（__保证修改之后的文件依旧为Unix格式__）

```text

;; run experimental FBReader for kindle
F R = !/mnt/us/fbKindle/goqt.sh FBReader &
;; unpack experimental fbreader's tar archive
U T = !cd /mnt/us; tar zxvf fbKindle-bin.tar.gz; rm fbKindle-bin.tar.gz; echo 101 >/proc/eink_fb/update_display
```

（4）、重启 Kindle。

（5）、在 Kindle 的 Home 页按 `Shift+U+T`（先按 Shift 键，接着是 U 和 T 键），此时 Kindle 的屏幕底部将显示`^[U T]`字样（不是在搜索框中哦）。稍等一会儿，fbKindle解压完成后，你将会看到Kindle变成灰屏，屏幕底部出现`success!`字样。至此，fbKindle 安装成功。按Home返回`Home`页。

（6）、按`Shift+F+R`就可以启动`fbKindle`了，屏幕底部出现`^[F R]`字样，然后是`success!`。如果要从fbKindle切换回Kindle，按`Alt+Shift`；反之也一样。退出fbKindle，则按`Alt+Back`。


## Kindle DXG的源代码

Kindle系列所有产品，使用的是嵌入式的Linux系统，而且代码是[开源的](http://www.amazon.com/gp/help/customer/display.html?ie=UTF8&nodeId=200203720)，如果你感兴趣，可以自己去研究代码。


## 扩展阅读

* [kindle dxg添加中文支持及更改字体,升级kindle3.2.1](http://www.wogong.net/blog/kindle-dxg-chinese-fonts-support/)
* [Kindle DX (Graphite) 系统升级和中文阅读优化](http://www.chiphell.com/thread-932614-1-1.html)
* [Kindle 3/DXG 3.2.1中文多字体解决方案（原创）](http://www.douban.com/group/topic/35831105/)
* [DXG折腾手记](http://www.douban.com/note/222361354/)
* [kindle 3 高级折腾教程](http://bbs.mydoo.cn/thread-19025-1-1.html)
* [kindle dx g 再刷一遍，越狱，字体破解，安装](http://www.douban.com/note/324434834/)
* [EPUB唯美字体包通用版2.0](http://www.hi-pda.com/forum/viewthread.php?tid=1110585&highlight=)
* [fbKindle：让Kindle变身ePub阅读器](http://bbs.mydoo.cn/thread-19463-1-1.html)

## 祝大家玩的开心

## 编程之道，就在[编程之美]

![编程之美](/img/weixin_qr.jpg)

