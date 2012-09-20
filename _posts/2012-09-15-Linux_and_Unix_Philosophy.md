---
layout: post
title: Unix的设计哲学
date: 2012-09-15
bookview: true
categories:
    - 书品
tags:
    - Unix
    - Linux
    - 设计
excerpt: <dl class="nr">
 <dt><img src="/img/book_covers/Linux_and_Unix_Philosophy.jpg"/> </dt>
 <dd>
 <p>Unix是一种设计精良的、高性能、高可靠的多用户高实时操作系统，是现代操作系统的鼻祖。她的成功研制，凝聚了几代人的心血。同时，也为现代高性能的通用后端系统设计积累了很多宝贵的经验。这些设计经验，甚至被*nix阵营称为“Unix的哲学”，系统设计的“黄金准则”。</p>
 </dd> </dl>
---

`Unix`是一种设计精良的、高性能、高可靠的多用户高实时操作系统，是现代操作系统的鼻祖。她的成功研制，凝聚了几代人的心血。同时，也为现代高性能的通用后端系统设计积累了很多宝贵的经验。这些设计经验，甚至被*\*nix阵营*称为“Unix的哲学”（当然也是和Windows阵营打口水仗的秘密武器），系统设计的“黄金准则”。

以下分享一些源自《Linux and Unix Philosophy》（Mike Gancarz著）一书的Unix设计原则，以供大家参考。如果想进一步了解Unix程序设计的原则，可以参考著名黑客`Eric S. Raymond`所著《The Art of Unix Programming》一书。

![Linux and Unix Philosophy](/img/book_covers/Linux_and_Unix_Philosophy.jpg)

## 19条Unix设计哲学

> <span class="badge badge-warning">1</span>: 小即是美（SMALL）

> <span class="badge badge-warning">2</span>: 让每一个程序只做好一件事（1 THING）

> <span class="badge badge-warning">3</span>: 尽快建立原型（PROTO）

> <span class="badge badge-warning">4</span>: 舍高效而取可移植性（PORT）

> <span class="badge badge-warning">5</span>: 使用纯文本文件来存储数据（FLAT）

> <span class="badge badge-warning">6</span>: 充分利用软件的杠杆效应（REUSE）

> <span class="badge badge-warning">7</span>: 使用Shell来提高杠杆效应及可移植性（SCRIPT）

> <span class="badge badge-warning">8</span>: 避免强制性的用户界面（NOCUI）

> <span class="badge badge-warning">9</span>: 让每一个程序都成为过滤器（FILTER）

> <span class="badge badge-warning">10</span>: 允许用户定制环境（custom）

> <span class="badge badge-warning">11</span>: 尽量使操作系统的内核小而轻巧（kernel）

> <span class="badge badge-warning">12</span>: 使用小写字母，并尽量保持简短（lcase）

> <span class="badge badge-warning">13</span>: 保护树木（trees）

> <span class="badge badge-warning">14</span>: 沉默是金（silence）

> <span class="badge badge-warning">15</span>: 并行思考（parallel）

> <span class="badge badge-warning">16</span>: 部分之和大于整体的效果（sum）

> <span class="badge badge-warning">17</span>: 寻找90%的解决方案（90cent）

> <span class="badge badge-warning">18</span>: 更坏的就是更好（worse）

> <span class="badge badge-warning">19</span>: 层次化思考（hier）

如果你了解*nix系统（如Linux），并从事过相关的开发工作，或多或少会了解这些原则。比如“沉默是金”这条原则，所表达的意思就是只有在程序出错的时候才打印反馈信息（然后终止处理），否则应该按流程正常“沉默”的执行。我拥有5年相关经验，深感这些原则的实用性。比如,“舍高效而取可移植性”这条原则，对很多人都可能没有指导意义，因为绝大部分的产品仅仅局限于某个特定的系统平台，于我则不同。大家都知道写一个Shell脚本很简单，但是你要写好一个跨平台的特定功能的Shell脚本可不是一件容易的事。写跨平台的C/C++程序一样会有这些问题（当然针对java会好一些，因为平台相关的东西都由JDK来解决）。这些方面我有非常丰富的经验，所以感触也很深。我曾经开发、维护的电信高性能业务管理系统，就用C/C++/Shell开发，公司的产品至少运行于Linux（Linux实际部署的，当然是64位刀片机.其他Unix平台都采用小型机。你懂的，如果大家打不通电话，充不了值等，那我们的麻烦就大了）、IBM AIX、HP-UX ia64、HP-UX 9000/800等平台，针对`可移植性`的开发投入是可想而知的。

在此不对其它原则一一展开叙述了，很多内容大家都可以联系出来。如果想进一步了解，建议你买本书看看，顺便支持一下开源运动（`当然，最好去实践这些原则，并将其有效的转化为实际的生产力`）。在开发设计时，多想想这些指导原则，相信大家都可以设计出精良的软件。


