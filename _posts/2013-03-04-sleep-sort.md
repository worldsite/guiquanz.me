---
layout: post
title: 天才排序算法：睡眠排序 
date: 2013-03-04
categories:
  - 技术
tags:
  - 睡眠排序
---
## Genius sorting algorithm: Sleep sort

2011年[4chan](http://dis.4chan.org/read/prog/1295544154)上有一个[Genius sorting algorithm: Sleep sort](http://dis.4chan.org/read/prog/1295544154)贴子很火爆。Sleep排序，弱爆了，绝对奇葩。(讨论很激烈，请去看原文)

核心思想很简单：`数字多大就休眠多长时间，然后打出数字来。排序过程自然完成`。以下是一个shell的实现版本（sleepsort.sh:此处稍有改动)。你若没看懂，别说自己会shell哦。

<pre class="prettyprint linenums">
#!/bin/sh

function f() {
  sleep "$1"
  echo "$1"
}
while [ -n "$1" ]
do
    f "$1" &
    shift
done
wait
echo "...Done..."
</pre>

### 运行方式

    sh ./sleepsort.sh 5 3 6 3 6 3 1 4 7


## 扩展阅读

* [天才排序算法：睡眠排序 ](http://blog.csdn.net/nash_/article/details/8514088)
* [Genius sorting algorithm: Sleep sort](http://dis.4chan.org/read/prog/1295544154)

