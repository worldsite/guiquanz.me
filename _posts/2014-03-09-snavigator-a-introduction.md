---
layout: post
title: snavigator-代码阅读的利器
date: 2014-03-09
categories:
  - 技术
tags:
  - snavigator
---
## snavigator

`snavigator`[http://sourceforge.net/projects/sourcenav/](http://sourceforge.net/projects/sourcenav/)是一块非常不错的开源软件，针对Linux平台c/C++代码阅读导航，可以与`Sourece Insight`媲美。只可惜sourceforge上的项目已经没更新了，好在代码还能用，网上能找到6.0的版本(sourcenav-6.0)。界面使用tk（tcl的图形编程框架）开发，虽不够华丽，也还不错，不妨一试。

[![sourcena](/img/article/2014-03/09-01_snavigator.png)](http://sourceforge.net/projects/sourcenav/)


## snavigator安装

sourcenav的安装也很简单，不再赘述。只是，需要修改tk/generic/tk.h的几行代码。具体修改，如下：

``` cpp
+++ ./tk/generic/tk.h
-#define VirtualEvent	    (LASTEvent)
-#define ActivateNotify	    (LASTEvent + 1)
-#define DeactivateNotify    (LASTEvent + 2)
-#define MouseWheelEvent     (LASTEvent + 3)
-#define TK_LASTEVENT	    (LASTEvent + 4)
+#define VirtualEvent	    (MappingNotify + 1)
+#define ActivateNotify	    (MappingNotify + 2)
+#define DeactivateNotify    (MappingNotify + 3)
+#define MouseWheelEvent     (MappingNotify + 4)
+#define TK_LASTEVENT	    (MappingNotify + 5)

 #define MouseWheelMask	    (1L << 28)
-
 #define ActivateMask	    (1L << 29)
 #define VirtualEventMask    (1L << 30)
-#define TK_LASTEVENT	    (LASTEvent + 4)
```

为`source navigator`创建`应用菜单`。具体操作，如下：

<pre class="prettyprint">
i@home> sudo gedit /usr/share/applications/source-navigator.desktop
[Desktop Entry]
Name=Source-Navigator
Comment=C/C++代码工具
Exec=/opt/sourcenav-6.0/bin/snavigator
Icon= 
Terminal=false
Type=Application
Categories=Application;Development;
</pre>

__注意：除了Icon一行之外，每行的后面不能有空格，否则菜单可能无法显示__


## 扩展阅读

* [source navigator NG is a source code analysis tool](http://sourcenav.berlios.de/)

## 祝大家玩的开心

## 编程之道，就在[编程之美]

![编程之美](/img/weixin_qr.jpg)

