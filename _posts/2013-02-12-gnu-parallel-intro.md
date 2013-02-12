--- 
layout: post
title: 巧用GNU Parallel实现作业并行化执行
date: 2013-02-12
categories:
  - 技术
tags:
  - Parallel
---

![](/img/article/2013-02/11-06.png)

GNU `parallel` 是一个可利用单机或多机`并行化`执行`作业`（job）的shell工具。针对作业的约束，如下：
一个作业，可以是`单条命令`或`一个小脚本（输入中每一行都必须被执行）`。最典型的输入是，一个文件列表，一个主机列表，一个用户列表，一个URL列表或一个数据表列表。

一个作业，也可以是从`管道`中读取的一个命令。此时，parallel可以切割`输入`，并行化的pipe到命令中（GNU parallel can then split the input and pipe it into commands in parallel）。

parallel可以很好的替代xargs 和 tee，以及shell中的各类loop操作（for等），且并行化执行的更快。

parallel的输出保持与原始命令的一致，所以可将其输出作为其它程序的输入。通常，parallel可以替代`xargs` 或 `cat |bash`之类的命令。


## 应用示例

1、批量修改`普通文件`的权限：

    find ./ -type f |parallel -m chmod 751

2、批量修改普`目录文件`的权限：

    find ./ -type d |parallel -m chmod 751

__没指定-m选项时，parallel不进行命令并行化，此时相当于for循环__

3、批量下载文件：

    （1）将以下的内容输入到batch.txt文件中：
     wget url1
     wget url2
     wget url3
     wget url4

    （2）执行命令：
     parallel -j+0 < batch.txt 

4、由于parallel本身就是一个脚本，所以可以直接在任务脚本中指定。如，

    #!/usr/bin/parallel 
    wget url1
    wget url2
    wget url3
    wget url4


## 安装parallel

    sudo yum install parallel


## 扩展资源

主页： [http://www.gnu.org/s/parallel](http://www.gnu.org/s/parallel)

文档： [http://www.gnu.org/s/parallel/man.html](http://www.gnu.org/s/parallel/man.html)


