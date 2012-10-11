--- 
layout: post
title: 期待Rebol的开源
date: 2012-09-29
categories:
  - 技术
tags:
  - Rebol
excerpt: <dl class="nr">
 <dt><img src="/img/article/rebol.gif"/> </dt>
 <dd>
 <p> 2012年9月25日，<a href="http://www.Rebol.com/article/0511.html">Carl Sassenrath</a>对外宣布计划在十月初R3发布之后开源<a href="http://Rebol.com/">Rebol</a>语言。目前正在进一步征集意见，预计采用GPL 2协议，并且同时会在<a href="https://www.github.com">GitHub</a>上释放代码。对很多人来说，期盼已久的事终于要到了。Rebol是一种特殊的SDL语言，WEB、GUI样样都能搞定，跨平台，关键是代码非常简单、明了，还不失优雅。据说其内核也非常精巧，不过几百k而已。期待，借此对Rebol进行深入的研究...</p>
 </dd> </dl>
---

## 缘由
2012年9月25日，Carl Sassenrath对外宣布计划在十月初R3发布之后[开源Rebol语言](http://www.Rebol.com/article/0511.html)。目前正在进一步征集意见，预计采用GPL 2协议，并且同时会在[Github](http://www.github.com)上释放代码。对很多人来说，期盼已久的事情终于要到了。Rebol是一种特殊的SDL语言，WEB、GUI样样都能搞定，跨平台，关键是代码非常简单、明了，还不失优雅。据说其内核也非常精巧，不过几百k的C代码而已。期待，借此对Rebol进行深入的研究。跟着牛人学习，进步会更快。

![强悍的编程能力](/img/article/rebol_e1.png)

## Rebol简介
[REBOL](http://Rebol.com/)发音 reb-ol \['reb-ol\]，是英文`R`elative `E`xpression `B`ased `O`bject `L`anguage的缩写。它是一种`基于相关表达式的对象语言`，结合了编程语言和元数据语言的特点（语言本身就是自己的`元语言`），具有`方言化`的功能，而且是针对分布式计算而设计的，由AmigaOS的架构师[Carl Sassenrath](http://en.wikipedia.org/wiki/Carl_Sassenrath),于1997年开发的。Rebol在往后的岁月有了很多发展，功能很强大，但依然是一门小众语言（或许和闭源等有关），估计很多人都不曾了解它。我也是一次偶然的机会在书店翻到`蔡学镛`先生的《`编程ING`》一书，才了解的（信息孤岛）。

## Rebol的特性

> 1. Rebol是, 一种基于相关表达式的对象语言，而不是一门普通的编程语言;
> 
> 2. Rebol是, 一种消息式的语言。主要目的是提供一种轻量级的分步计算和通讯的方法；
> 
> 3. Rebol不仅仅只是一个编程语言。还是一种用于表示数据和元数据的语言。给计算、存储和信息交换提供了同一种方法；
> 
> 4. Rebol有十分丰富的软件包。初学者可以从Rebol/Core和Rebol/View开始。专业人士会发现Rebol/Command和Rebol/SDK十分有用。而Rebol/IOS是一个强大的协作交流平台；
> 
> 5. Rebol的可移植性非常好。其代码和数据跨越了40多种平台。在Windows上写的脚本，在*Nix和其它平台上运行都一样，根本无须任何改动；
> 
> 6. Rebol引入了`方言化`的概念（这个概念最早应该在`Lisp`中出现。比如，`Scheme`就是Lisp的一种方言）。方言是一个小巧有效专门针对特殊领域的子语言；
> 
> 7. Rebol本意就是要保持小巧。即使已经包含了数百个函数，几十种数据类型，内置帮助，多种Internet协议、压缩、错误处理、调试控制台、加密和更多；
> 
> 8. Rebol程序很容易书写。你所需要的仅仅是一个文本编辑器。一个程序可以只有一行也可以是几十个文件。同时，程序不需要对库和包含做声明；
> 
> 9. 等等。

## 编程示例

*1. 经典例子:*
<pre class="prettyprint linenums">
print "Hello World!"
</pre>

*2. 摘录并打印网页的标题：*
<pre class="prettyprint linenums">
page: read http://www.cnet.com
parse page [thru <title> copy title to </title>]
print title
</pre>

*3. 简单的图形化界面：*
<pre class="prettyprint linenums">
REBOL [Title: "Example"]
print "Hello World!"
halt
</pre>

*4. 一个简单的Web服务器:*

    REBOL [Title: "Tiny Web Server"]

    web-dir: %.   ; the path to where you store your web files

    listen-port: open/lines tcp://:80  ; port used for web connections

    errors: [
        400 "Forbidden" "No permission to access:"
        404 "Not Found" "File was not found:"
    ]

    send-error: function [err-num file] [err] [
        err: find errors err-num
        insert http-port join "HTTP/1.0 " [
            err-num " " err/2 "^/Content-type: text/html^/^/" 
            <HTML> <TITLE> err/2 </TITLE>
            "<BODY><H1>SERVER-ERROR</H1><P>REBOL Webserver Error:"
            err/3 " " file newline <P> </BODY> </HTML>
        ]
    ]

    send-page: func [data mime] [
        insert data rejoin ["HTTP/1.0 200 OK^/Content-type: " mime "^/^/"]
        write-io http-port data length? data
    ] 

    buffer: make string! 1024  ; will auto-expand if needed

    forever [
        http-port: first wait listen-port
        clear buffer
        while [not empty? request: first http-port][
            repend buffer [request newline]
        ]
        repend buffer ["Address: " http-port/host newline] 
        print buffer
        file: "index.html"
        mime: "text/plain"
        parse buffer ["get" ["http" | "/ " | copy file to " "]]
        parse file [thru "." [
                "html" (mime: "text/html") |
                "gif"  (mime: "image/gif") |
                "jpg"  (mime: "image/jpeg")
            ]
        ]
        any [
            if not exists? web-dir/:file [send-error 404 file]
            if error? try [data: read/binary web-dir/:file] [send-error 400 file]
            send-page data mime
        ]
        close http-port
    ]


## 扩展阅读

1. [Rebol官网](http://rebol.com)；
2. [Rebol文档](http://www.rebol.com/docs.html);
3. [更多Rebol编程示例](http://www.rebol.net/cookbook)。

