---
layout: post
title: 14条高性能Web设计原则
date: 2012-09-15
bookview: true
categories:
    - 书品
tags:
    - web
    - 设计
excerpt: <dl class="nr">
 <dt><img src="/img/book_covers/high_performance_web_sites.png"/> </dt>
 <dd>
 <p>分享源于Yahoo工程师Steve Souders和Nate Koechley所著《High Performance Web Sites》一书中提到的14条性能方面的设计原则，希望大家以此共勉，并不断创新。虽然是2007年出版的书，但这些原则都是大牛们大量实践的宝贵结晶，放在当下依然有非常高的价值。</p>
 </dd> </dl>
---
## 缘由

最近在着手做一些有趣的产品，顺便把曾经熟悉的web前端开发相关技术捡回来（这几年一直都在从事移动通信后端的大型系统开发及维护）。大家都希望做一流的，高性能的网站，除了后端高可靠、高性能的架构支撑，前端的优化也是必不可少的。以下分享源于Yahoo工程师`Steve Souders`和`Nate Koechley`所著《`High Performance Web Sites`》一书中提到的14条性能方面的设计原则，希望大家以此共勉，并不断创新。虽然是2007年出版的书，但这些原则都是大牛们大量实践的宝贵结晶，放在当下依然有非常高的价值。
![High Perforrmance Web Sites](/img/book_covers/high_performance_web_sites.png)

## 14条高性能Web设计原则

> <span class="badge badge-info">1</span>: Make Fewer HTTP Requests

> <span class="badge badge-info">2</span>: Use a Content Delivery Network

> <span class="badge badge-info">3</span>: Add a Expores Header

> <span class="badge badge-info">4</span>: Gzip Components

> <span class="badge badge-info">5</span>: Put Stylesheets at the Top

> <span class="badge badge-info">6</span>: Put Scripts at the Bottom

> <span class="badge badge-info">7</span>: Avoid CSS Expressions

> <span class="badge badge-info">8</span>: Make JavaScript add Css External

> <span class="badge badge-info">9</span>: Reduce DNS Lookups

> <span class="badge badge-info">10</span>: Minify JavaScript

> <span class="badge badge-info">11</span>: Avoid Redirects

> <span class="badge badge-info">12</span>: Remove Duplicate Scripts

> <span class="badge badge-info">13</span>: Configure Etags

> <span class="badge badge-info">14</span>: Make Ajax Cacheable

这些都是很容易就能达成的设计要求。如果你的产品还是逻辑与表现混杂等等，建议你尽快尝试一下，定有很多惊喜哦。


