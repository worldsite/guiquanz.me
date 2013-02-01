--- 
layout: post
title: 巧用Graphviz绘制状态机图
date: 2013-01-21
categories:
      - 技术
tags:
      - Graphviz
---

![](/img/article/graphviz.png)

我（Unix系）是习惯于用`文本`这种简单、古董的格式保存数据文件，便于传输、分享等。[graphviz](http://www.graphviz.org/)是一个非常棒的工具集，用于特殊文本数据的可视化，出自`AT&T实验室`。graphviz的主要组件，如下：

`dot`: "hierarchical" or layered drawings of directed graphs. This is the default tool to use if edges have directionality.

`neato`: "spring model'' layouts.  This is the default tool to use if the graph is not too large (about 100 nodes) and you don't know anything else about it. Neato attempts to minimize a global energy function, which is equivalent to statistical multi-dimensional scaling.

`fdp`: "spring model'' layouts similar to those of neato, but does this by reducing forces rather than working with energy.

`sfdp`: multiscale version of fdp for the layout of large graphs.

`twopi`: radial layouts, after Graham Wills 97. Nodes are placed on concentric circles depending their distance from a given root node.

`circo`: circular layout, after Six and Tollis 99, Kauffman and Wiese 02. This is suitable for certain diagrams of multiple cyclic structures, such as certain telecommunications networks.

以下介绍，用其中的dot工具绘制有限状态机图。


## 非确定性有限状态机

1.编辑finite_state_machine.gv文件。具体内容，如下：

    digraph finite_state_machine {
      rankdir=LR;
      size="8,5"

      node [shape=doublecircle color="red"] S;
      node [shape=point] qs;
      node [shape=circle color="green"] q1;
      node [shape=circle color="red"] q2;

      qs -> S;
      S  -> q1 [label="a"];
      S  -> S  [label="a"];
      q1 -> S  [label="a"];
      q1 -> q2 [label="b"];
      q2 -> q1 [label="b"];
      q2 -> q2 [label="b" color="blue"];
    }

2.执行命令，生成图像

    dot -Tpng finite_state_machine.gv -o finite_state_machine.png

3.图像效果，如下：

![](/img/article/2013-01-21-01-finite_state_machine.png)


## 确定性有限状态机

1.编辑definite_state_machine.gv文件。具体内容，如下：

    digraph definite_state_machine {
      rankdir=LR;
      size="8,5"
       
      node [shape=doublecircle label="{f}", fontsize=12] f;
      node [shape=doublecircle label="{q2, f}", fontsize=10] q2f;
      node [shape=circle label="S", fontsize=14] S;
      node [shape=circle, label="{q1}", color="red", fontsize=12] q1;
      node [shape=circle, label="{q2}", fontsize=12] q2;
      node [shape=point] qs;
       
      qs -> S;
      S  -> q1  [label="a"];
      S  -> q2  [label="b"];
      S  -> q2f [label="c"];
        
      q1  -> q2 [label="b"];
      q2f -> f  [label="b"];
      q2f -> q2 [label="c"];
        
      q2 -> f  [label="b"]
      q2 -> q2 [label="c" color="blue"];
    }

2.执行命令，生成图像

    dot -Tpng definite_state_machine.gv -o definite_state_machine.png

3.图像效果，如下：

![](/img/article/2013-01-21-02-definite_state_machine.png)


## 扩展阅读

1.[Graphviz Node Shapes](http://www.graphviz.org/doc/info/shapes.html)

2.[The DOT language](http://www.graphviz.org/content/dot-language)

3.[An Introduction to GraphViz](http://www.linuxjournal.com/article/7275?page=0,0)


