---
layout: post
title:  Source Sans 开源字体
date: 2015-05-26
categories:
  - 技术
tags:
  - 字体
---
##  Source Sans 字体

2014年 Adobe 和 Google 公司联合发布了一个针对 CJK （中日韩）文的开源字体 [Source Sans](https://typekit.com/fonts/source-sans-pro) (Google发布的字体名称是 [Noto Fonts](https://www.google.com/get/noto/#/) (2015年 Google 又开源了 [Roboto](https://github.com/google/roboto)字体)) , 中文名称为 `思源字体` 引起了广泛的关注。

`思源黑体`，这一字体家族有七种字体粗细，完全支持日文、韩文、繁体中文和简体中文，这些全部都包含在一种字体中。它还包括来自我们颇受欢迎的 Source Sans字体家族的拉丁文、希腊文和西里尔文字形。总的来说，字体家族里的每种字体粗细总共有 65,535个字形（OpenType 格式支持的最大上限），而整个字体家族的字形个数接近50万。这个字体家族是通过开源方式提供的，数量、开发规模和价值之大堪称史上之最，这使它成为需要支持多种语言的字体的设计人员、开发人员和普通用户的零成本解决方案。

[西塚涼子](http://www.adobe.com/products/type/font-designers/ryoko-nishizuka.html)是字体小组的高级设计师，这一新字体家族的基础设计就出自她之手。关键要求令人望而却步：该字体家族需要涵盖上述多种语言，还需要支持来自使用这些语言的地区的区域性字形变体。在某些情况下，基于一种原始中国象形文字的字形可能有多达四种区域性变体。还有一点很重要，那就是字体能呈现出卓越的打印效果，且能在当今常用的多种平板电脑和移动设备的屏幕上有完美表现。

凉子创造了一种风格相对现代化的字体，这种字体笔触简单、质量一致。这使得它在平板电脑和智能手机等小型设备上具有更好的可读性。尽管它经过精简，但却很大程度上保留了传统无衬线 (Sans Serif) 字体设计的优雅，使得软件菜单中包含单行文字或短语的文本以及电子书中较长的文本块具有很高的可读性。

最后，由 [Ken Lunde](http://blogs.adobe.com/CCJKType/) 博士，统一整合处理。Ken 是世界知名的 CJKV 字体专家，他负责字形集和 Unicode 映射的指定、与合作伙伴互动并整合他们的字形内容，以及最终字体源的创建。Ken 。

思源黑体支持以下字体粗细：ExtraLight、Light、Normal、Regular、Medium、Bold 和 Heavy。在您订阅任何级别的 Typekit 套餐（包括免费级别的套餐）之后，即可在桌面上使用它，且可同步它以在任何桌面应用程序中使用。如果您之前未尝试过桌面字体同步，没关系，这很简单；Greg Veen  在此视频中演示了同步过程。此外，关于如何使用Typekit 字体同步的详细信息提供了简体中文、繁体中文、日文和韩文版。

思源黑体，根据 Apache 2.0 许可提供授权。完整版多语种字体家族、单语言子集和源文件都可下载。


BTW: 突然觉得，NB的公司总能干成NB的事，其实这事本该是大宋政府的分内事，却被这两家公司搞定了。真不容易。

号外：[Fontmin - 第一个纯JavaScript字体子集化方案](http://ecomfe.github.io/fontmin/#banner)也发布了。这是，来自百度的前端设计团队，效果非常好。


## 扩展阅读

* [Fontmin - 第一个纯JavaScript字体子集化方案](http://ecomfe.github.io/fontmin/#banner)
* [fontmin-app](https://github.com/ecomfe/fontmin-app/releases)
* [fontmin 开源代码库地址](https://github.com/ecomfe/fontmin)
* [一款Pan-CJK 开源字体](http://blog.typekit.com/alternate/source-han-sans-chs/)
* Adobe [Source Sans](https://typekit.com/fonts/source-sans-pro/)
* Google [Noto Fonts](https://www.google.com/get/noto/)
* Google [Roboto](https://github.com/google/roboto) 字体源代码库
* Google [Roboto Fonts](http://developer.android.com/design/style/typography.html)
* [使用思源黑体](http://www.iinterest.net/2015/01/29/%E4%BD%BF%E7%94%A8%E6%80%9D%E6%BA%90%E9%BB%91%E4%BD%93/)
* [Google中日韩字体Noto Sans CJK让你的阅读体验更佳](http://googlechinablog.blogspot.com/2014/07/googlenoto-sans-cjk.html)
* [通用规范汉字表](http://zh.wikipedia.org/wiki/%E9%80%9A%E7%94%A8%E8%A7%84%E8%8C%83%E6%B1%89%E5%AD%97%E8%A1%A8) 和 [PDF](http://vdisk.weibo.com/s/Dpgxfnvip-9u?from=page_100505_profile&wvr=6) 文档
* [如何将Google Chrome字型设为思源黑体？](http://www.chinaz.com/web/2014/0729/361593.shtml)
* [如何将 OS X 系统介面的 CJK 字体全部换成思源黑体？](http://www.zhihu.com/question/24534869)
* [网页使用思源字体](http://www.phodal.com/blog/use-source-han-sans-cn-in-css/)
* [思源黑體修改版「思源柔黑體」字型](http://jikasei.me/font/genjyuu/)
* [修改 Mac OS X 10.9 Mavericks 之预设中文字体为思源黑体教程](http://www.arefly.com/change-os-x-10-9-default-chinese-font-to-noto/)
* [Chinese Standard Web Fonts: A Guide to CSS Font Family Declarations for Web Design in Simplified Chinese](http://www.kendraschaefer.com/2012/06/chinese-standard-web-fonts-the-ultimate-guide-to-css-font-family-declarations-for-web-design-in-simplified-chinese/)
* [中文字体网页开发指南](http://www.ruanyifeng.com/blog/2014/07/chinese_fonts.html)
* [IDS + OpenType: Pseudo-encoding Unencoded Glyphs](http://blogs.adobe.com/CCJKType/2014/03/ids-opentype.html)


## 祝大家玩的开心

## 编程之道，就在[编程之美]

![编程之美](/img/weixin_qr.jpg)

