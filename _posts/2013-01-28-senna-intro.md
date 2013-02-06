--- 
layout: post
title: SENNA——一块不错的开源机器学习软件
date: 2013-01-28
categories:
  - 技术
tags:
  - SENNA
---

## SENNA简介

![](/img/article/senna.jpg)

2013年伊始机器学习（ML）和DL（深度学习）非常火爆，引爆了微薄很多热点。`SENNA`，出自[NEC Laboratories America, Inc](http://www.nec-labs.com/research/machine/ml_website/contents_page.php?content=software)（有很多ML相关的研究成果，如[Torch](http://www.nec-labs.com/research/machine/ml_website/content.php?content=software&id=torch&display=html_long)等），实现很精巧，大概3500多行ANSI C代码，适合各类研究使用。作为机器学习的不错的工具，需要时可以好好研究一下。具体，如下：

[SENNA](http://ml.nec-labs.com/senna/) is a software distributed under a [non-commercial license](http://ml.nec-labs.com/senna/license.html), which outputs a host of Natural Language Processing (`NLP`) predictions: part-of-speech (`POS`) tags, chunking (`CHK`), name entity recognition (`NER`), semantic role labeling (`SRL`) and syntactic parsing (`PSG`).

SENNA is fast because it uses a simple architecture, self-contained because it does not rely on the output of existing NLP system, and accurate because it offers state-of-the-art or near state-of-the-art performance.

SENNA is written in ANSI C, with about 3500 lines of code. It requires about 200MB of RAM and should run on any IEEE floating point computer.

Proceed to the download page. Read the compilation section in you want to compile SENNA yourself. Try out a sanity check. And read about the usage.

New in SENNA v3.0 (August 2011)
Here are the main changes compared to SENNA v2.0:

Syntactic parsing.
We now include our original word embeddings, used to trained each task.
Bug correction: now outputs correctly tokens made of numbers (instead of replacing numbers by "0").
Option -offsettags, which outputs start/end offsets (in the sentence) of each token.


## 扩展阅读

1.[Natural Language Processing (Almost) from Scratch](http://ronan.collobert.com/pub/matos/2011_nlp_jmlr.pdf)

2.[Deep Learning for Efficient Discriminative Parsing](http://ronan.collobert.com/pub/matos/2011_parsing_aistats.pdf)

3.[SHOGUN（将军）: A large scale machine learning toolbox](http://www.shogun-toolbox.org/)


