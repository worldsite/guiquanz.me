--- 
layout: post
title: 巧用keydown和markdown编写基于文本的幻灯片
date: 2013-01-31
categories:
  - 写作
tags:
  - keydown
  - markdown
---

![](/img/article/keydown.png)

## 前提条件

* 安装了 Ruby 环境： sudo yum install ruby gem
* 安装了 keydown 工具： sudo gem install keydown
* 了解 markdown 语法： [Markdown 语法](http://wowubuntu.com/markdown)
* etao体验工作平台的文章：[Markdown - 引领未来科技写作的博客利器](http://ux.etao.com/posts/620)

## keydown简介

[KeyDown](https://github.com/infews/keydown)，是基于`Markdown`和 [deck.js](http://imakewebthings.github.com/deck.js) 的用ruby开发的“单页HTML讲义（给予浏览器的幻灯片）”的工具。它方便我们创建依赖于`deck.js`的工程，编写Markdown文档，最后将Markdown文档转化为HTML网页。 Showoff, Slidedown, HTML5 Rocks, with a little Presentation Zen thrown in.

示例地址：http://infews.github.com/keydown/#slide-0


## 基本应用

### 创建模板工程

    $ keydown generate <工程名称>

会创建以下目录（以test为工程名称示例）:

    | - test/
      | - css/               - Keydown CSS and a file for you to customize
      | - deck.js/
      | - images/            - Some Keydown images, but also for you
      | - js/                - Keydown JavaScript, and a file for you to customize
      | - slides.md

### 用Markdown语法，修改你的幻灯片

编辑`slides.md`文件（需要`切换到test目录`），内容如下：

    !SLIDE
    
    # This is my talk
    
    !SLIDE
    
    ## I hope you enjoy it
    
    !SLIDE code
    
        def foo
          :bar
        end
    
	!NOTES
	
	  * make sure to explain the use of Ruby symbols	
	
    !SLIDE
    
    Google is [here](http://google.com)
    
    !SLIDE
    
    # Questions?

生成HTML文档时，`!NOTE` 会被忽略掉。

### 生成HTML 幻灯片文件

    $ keydown slides slides.md

..会生成 `slides.html`文件

### 浏览结果

通过浏览器打开`slides.html`文件:

  * 通过 left, right键，播放幻灯片


## KeyDown使用说明

### 幻灯片的标题

在第一个`!SLIDE`之前出现的`H1`会被当作最终生成的单页HTML的`<title>`，也就是标题。标题属于可选内容。

### 幻灯片类型

`!SLIDE`之后的任何文本（此处指的是`单行内容`），都将归为CSS类（指HTML文档中的CSS内容）。如：

    !SLIDE dark

可以定义自己的CSS类型。

### 图片 

最终生成的HTML文档与Markdown文件在相同的目录，所以图片URI采用相对路径。

### 全屏背景图

Keydown支持幻灯片`全屏背景图`以及`文本属性`。具体，如下：

    !SLIDE
    
    # This slide has a background image
    
    }}} images/sunset.jpg

### 语法高亮

通过[CodeMirror](http://codemirror.net)实现代码的高亮现实。

需要高亮显示的代码以`@@@` or ` ``` `为开始、结束标识，前后需要一致（不能换用）。

如Ruby高亮:

    @@@ ruby
        def foo
          :bar
        end
    @@@

如JavaScript高亮

    ``` js
        function foo() {
          return 'bar';
        }
    ```

### 定制 CSS

`css` 目录下的文件，将链进你的HTML文件。

### 定制 JavaScript

`js` 目录下的JavaScript文件，将链进你的HTML文件。

### Deck.js 扩展

工程中任何对CSS 和 JS 的扩展都将加载的最终生存的幻灯片中。


## 类似工具

* [Showoff](http://github.com/drnic/showoff)
* [Slidedown](http://github.com/nakajima/slidedown)
* [HTML5 Rocks](http://studio.html5rocks.com/#Deck)
* [Presentation Zen](http://amzn.to/8X55H2)


## 支持HTML 幻灯片制作的JS库

* [deck.js](http://imakewebthings.github.com/deck.js)
* [impress.js](bartaz.github.com/impress.js)
* [reveal.js](https://github.com/hakimel/reveal.js)，及[http://lab.hakim.se/reveal-js](http://lab.hakim.se/reveal-js)


## 扩展阅读

* [Markdown: Syntax](http://daringfireball.net/projects/markdown/syntax)
* [繁体版Markdown语法](http://markdown.tw)

