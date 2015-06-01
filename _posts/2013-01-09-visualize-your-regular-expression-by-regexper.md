--- 
layout: post
title: 巧用RegExper可视化呈现正则表达式
date: 2013-01-09
categories:
  - 技术
tags:
  - 正则表达式
  - RegExper
---

## 你是否还再为分析、构建复杂的正则表达式烦恼？

![](/img/article/regexper/demo1.png)

正则表达是，软件开发、维护人员`必须`掌握的实用技术之一（__如果你还不会，小心屁股挨板子哦__）。在`grep`中、`awk`，`sed`里，经常有她的倩影。在`python`、`ruby`中，也会时常出现……。其实，`perl`才是她的据点。正则表达式很美，她无处不在（日志提取、分析，词法分析等等）。

可是，面对复杂的表达式时，你有过抓狂的时候吗？下面给你介绍一块实用工具RegExper，它可以帮你可视化正则表达式。


## RegExper简介

[RegExper](http://www.regexper.com/)是，一块开源工具软件。基于`javascript`和`ruby`实现，提供B/S模式的正则表达式的可视化平台，方便各种分析使用。可以通过访问[http://www.regexper.com](http://www.regexper.com),尝试分析各种场景。

RegExper，已在`github`上开源。项目地址为[https://github.com/javallone/regexper](https://github.com/javallone/regexper)。可以自己折腾代码咯。

![](/img/article/regexper/demo2.png)

__**备注:**__ 目前regexper不支持分析结果（png等格式）的导出操作。以后应该会有改善。


另外 [regulex](http://jex.im/regulex/) 也是一个针对JavaScript的不错的同类工具。

![](/img/article/11/2014-11-28-02.png)


还有 [http://regexr.com/](http://regexr.com/) 也不错哦。


## 扩展阅读

* "perlre - Perl regular expressions" [http://perldoc.perl.org/perlre.html](http://perldoc.perl.org/perlre.html)
* "Implementing Regular Expressions" [http://swtch.com/~rsc/regexp](http://swtch.com/~rsc/regexp)
* The re1 project: [http://code.google.com/p/re1](http://code.google.com/p/re1)
* The re2 project: [http://code.google.com/p/re2](http://code.google.com/p/re2)
* [RegExper](http://www.regexper.com/)
* [regulex](http://jex.im/regulex/)
* [http://regexr.com/](http://regexr.com/)

