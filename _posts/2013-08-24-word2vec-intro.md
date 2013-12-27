---
layout: post
title: word2vec,深度机器学习库
date: 2013-08-24
categories:
  - 技术
tags:
  - 待定标签
---
## 主题简介

[word2vec](https://code.google.com/p/word2vec/)

## 扩展阅读

* [Google开源基于Deep Learning的word2vec工具](http://www.csdn.net/article/2013-08-20/2816643-word2vec)

word2vec（word to vector）顾名思义，这是一个将单词转换成向量形式的工具。通过转换，可以把对文本内容的处理简化为向量空间中的向量运算，计算出向量空间上的相似度，来表示文本语义上的相似度。

word2vec为计算向量词提供了一种有效的连续词袋（bag-of-words）和skip-gram架构实现，word2vec遵循Apache License 2.0开源协议。

如何转换？

word2vec主要是将文本语料库转换成词向量。它会先从训练文本数据中构建一个词汇，然后获取向量表示词，由此产生的词向量可以作为某项功能用在许多自然语言处理和机器学习应用中。

在举例子之前，引入余弦距离（Cosine distance）这个概念（摘自维基百科）：

通过测量两个向量内积空间的夹角的余弦值来度量它们之间的相似性。0度角的余弦值是1，而其他任何角度的余弦值都不大于1;并且其最小值是-1。从而两个向量之间的角度的余弦值确定两个向量是否大致指向相同的方向。两个向量有相同的指向时，余弦相似度的值为1；两个向量夹角为90°时，余弦相似度的值为0；两个向量指向完全相反的方向时，余弦相似度的值为-1。在比较过程中，向量的规模大小不予考虑，仅仅考虑到向量的指向方向。余弦相似度通常用于两个向量的夹角小于90°之内，因此余弦相似度的值为0到1之间。
然后可以通过distance工具根据转换后的向量计算出余弦距离，来表示向量（词语）的相似度。例如，你输入“france”，distance工具会计算并显示与“france”距离最相近的词，如下：

              Word             Cosine distance
      -------------------------------------------
                spain              0.678515
              belgium              0.665923
          netherlands              0.652428
                italy              0.633130
          switzerland              0.622323
           luxembourg              0.610033
             portugal              0.577154
               russia              0.571507
              germany              0.563291
            catalonia              0.534176
在word2vec中主要有两种学习算法：连续词袋和连续skip-gram，switch-cbow允许用户选择学习算法。这两种算法有助于预测其它句子的词汇。
从词转换到句子或更长的文本

在一些特定的应用程序中，它还可以用于多个词汇，例如，“san francisco”，这样它就会通过预先处理数据集，让其形成句子，找到与“san francisco”余弦距离最近的内容：

              Word          Cosine distance
-------------------------------------------
          los_angeles              0.666175
          golden_gate              0.571522
              oakland              0.557521
           california              0.554623
            san_diego              0.534939
             pasadena              0.519115
              seattle              0.512098
                taiko              0.507570
              houston              0.499762
     chicago_illinois              0.491598
如何衡量词向量质量
可能影响到词向量质量的几个因素：

训练数据的数量和质量
向量的大小
训练算法
向量的质量对任何一个应用程序都非常重要，然而，根据复杂的任务来探索不同的超参数设置可能会过于苛刻。因此，我们设计了简单的测试集，来快速评估矢量词的质量。

词聚类（Word clustering）

词向量也可以从巨大的数据集中导出词类，通过执行词向量顶部的K-means聚类即可实现，脚本演示地址：./demo-classes.sh，最后输出的是一个词汇表文件和与之对应的类ID标识，例如：

carnivores 234
carnivorous 234
cetaceans 234
cormorant 234
coyotes 234
crocodile 234
crocodiles 234
crustaceans 234
cultivated 234
danios 234
.
.
.
acceptance 412
argue 412
argues 412
arguing 412
argument 412
arguments 412
belief 412
believe 412
challenge 412
claim 412
性能
在多核CPU上（使用开关‘-threads N’），通过使用并行训练可以显著提高训练速度，超参数选择对性能也至关重要（包括速度和准确率），主要选择有：

架构：skip-gram（慢、对罕见字有利）vs CBOW（快）
训练算法：分层softmax（对罕见字有利）vs 负采样（对常见词和低纬向量有利）
欠采样频繁词：可以提高结果的准确性和速度（适用范围1e-3到1e-5）
维度词向量：通常情况下表现都很好
文本（window）大小：skip-gram通常在10附近，CBOW通常在5附近
去哪里收集训练数据

随着训练数据的增加，词向量质量也显著提升，如果以研究为目的的，可以考虑线上数据集：

来自维基百科的上亿字符（在Matt Mahoney页面底部，适用预处理perl脚本）
WMT11网站：多语言的大量文本数据。
快速入门

代码下载：http://word2vec.googlecode.com/svn/trunk/
运行“make”编译word2vec工具
运行demo脚本：./demo-word.sh and ./demo-phrases.sh
关于word2vec更多介绍，大家可以阅读：https://code.google.com/p/word2vec/

## 祝大家玩的开心

