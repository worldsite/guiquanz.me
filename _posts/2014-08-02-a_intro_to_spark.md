---
layout: post
title: Spark - 分布式计算无痛上手指南
date: 2014-08-02
categories:
  - 技术
tags:
  - 待定标签
---
## 主题简介

http://spark.apache.org/docs/latest/quick-start.html

export SBT_HOME=/home/sudoku/ruby/Spark/sbt
export SBT_BIN=$SBT_HOME/bin
export PATH=$PATH:$SBT_BIN

export SCALA_HOME=/home/sudoku/ruby/Spark/scala-2.10.4
SCALA_BIN=$SCALA_HOME/bin
export PATH=$PATH:$SCALA_BIN


./sbt/sbt package
./sbt/sbt clean compile
./sbt/sbt assembly

-----
https://github.com/apache/spark/blob/master/bin/pyspark

# Exit if the user hasn't compiled Spark
if [ ! -f "$FWDIR/RELEASE" ]; then
  # Exit if the user hasn't compiled Spark
  ls "$FWDIR"/assembly/target/scala-$SCALA_VERSION/spark-assembly*hadoop*.jar >& /dev/null
  if [[ $? != 0 ]]; then
    echo "Failed to find Spark assembly in $FWDIR/assembly/target" 1>&2
    echo "You need to build Spark before running this program" 1>&2
    exit 1
  fi
fi

-------------
http://www.scala-lang.org/files/archive/scala-2.10.4.tgz


http://www.scala-sbt.org/0.13/tutorial/sbt-tutorial.pdf


## 扩展阅读

* [Spark Programming Guide](http://spark.apache.org/docs/latest/programming-guide.html)
* [Scala for Programming Beginners](http://www.scala-lang.org/documentation/getting-started.html)
* [Effective Scala](http://twitter.github.io/effectivescala/index-cn.html)
* [Scala 课堂!](https://twitter.github.io/scala_school/zh_cn/index.html)
* [Scala Source code](https://github.com/scala/scala)
* [Getting Started with sbt](http://www.scala-sbt.org/0.13/tutorial/sbt-tutorial.pdf)


## 祝大家玩的开心

## 编程之道，就在[编程之美]

![编程之美](/img/weixin_qr.jpg)

