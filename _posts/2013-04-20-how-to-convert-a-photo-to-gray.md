---
layout: post
title: 巧用ImageMagick，将图片转化为灰底
date: 2013-04-21
categories:
  - 技术
tags:
  - convert
---
## 缘由

`2013年04月20日`，又是一个必须铭记的日子。`四川雅安`，爆发了`7.0`级地震，截至目前报告死亡人数`179`人，失踪`7000`多人……让我们祝福雅安，祝愿大家平安！`无雅不安`！

为表哀思，很多网站开始变灰了。可是，如果你事先没有准备一套灰色背景的图标，该怎么办呢？操起PS，重新设计一套？这个可行，不过你会累垮掉的，如果图片很多。作为开发人员，第一个应该想到的是：用ImageMagick（网站图像命令行处理的利器呀）的`convert`工具，批量处理图片。

具体怎么做呢？这里举一个小例。至于批量处理，只需要定制一个简单的shell脚本即可，不再赘述。

![](/img/book_covers/high_performance_web_sites.png)

通过执行以下命令，将上图转化为下图：
     
    convert a.png -colorspace gray b.png
     
![](/img/article/2013-04/20-03.png)


## 扩展阅读

* [ImageMagick: Convert, Edit, Or Compose Bitmap Images](http://www.imagemagick.org/script/index.php)
* [ImageMagick v6 Examples -- Color Modifications](http://www.imagemagick.org/Usage/color_mods/)
* [我的ImageMagick使用心得](http://www.charry.org/docs/linux/ImageMagick/ImageMagick.html)


## 祝大家玩的开心

