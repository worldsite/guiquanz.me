--- 
layout: post
title: 仅需一行代码，将chrome打造成一个编辑器
date: 2013-02-06
categories:
  - 技术
tags:
  - chrome
  - javascript
---

## 先看效果吧！

![](/img/article/chrome/chrome-editor.png)

如果告诉你，“将[以下这行代码](https://gist.github.com/minikomi/4672169)复制到chrome的地址框内，并按一下回车，你的chrome就成了一个不错的编辑器”（上图就是一个简单效果展示），你会怎么想？

    data:text/html,
    <style type="text/css">
    #e {
      position:absolute;
      top:0;
      right:0;
      bottom:0;
      left:0;
      font-size:16px;
    }
    </style>
    <div id="e"></div>
    <script src="http://d1n0x3qji82z53.cloudfront.net/src-min-noconflict/ace.js"></script>
    <script src="http://code.jquery.com/jquery-1.9.0.min.js"></script>
    <script>
    var myKey="SecretKeyz";
    $(document).ready(function(){
        var e;
        var url = "http://api.openkeyval.org/"+myKey;
        $.ajax({
          url: url,
          dataType: "jsonp",
          success: function(data){
           e = ace.edit("e");
           e.setTheme("ace/theme/tomorrow_night_eighties");
           e.getSession().setMode("ace/mode/markdown");
           e.setValue(data);
          }
        });
    $("#e").on("keydown", function (b) {
          if (b.ctrlKey && 83 == b.which) {
            b.preventDefault();
            var data = myKey+"="+encodeURIComponent(e.getValue());
            $.ajax({
              data: data,
              url: "http://api.openkeyval.org/store/",
              dataType: "jsonp",
              success: function(data){
                alert("Saved.");
              }
            });
          }
        });
    });
    </script>

这是最近比较火爆的一行代码。起因，如下：

程序员Jose Jesus Perez Aguinaga在 [CoderWall](https://coderwall.com/p/lhsrcq) 分享了一个小技巧：在浏览器地址栏中输入一行代码：
`data:text/html, <html contenteditable>`，回车即可把浏览器变临时编辑器（需要浏览器支持 HTML5 属性 contenteditable）。不少人受Jose的启发，开始对这行代码进行hacking，如改成支持[Ruby语法高亮的编辑器](https://gist.github.com/jakeonrails/4666256)……

经过不断改造，这行代码，逐步让chrome变成了包括支持Java、Ruby、Python等多种编程语言高亮的代码编辑器。已经出现使用第三方网站数据库API服务存储内容的[在线编辑器](https://gist.github.com/minikomi/4672169)了，与[notepad.cc](http://notepad.cc)网站功能相近。当然，有人已将其实现为[chrome的扩展插件](https://chrome.google.com/webstore/detail/editor-in-chrome/kekmncaadcnnngadocpbcfaaaldklmnd)了。插件效果，如下：（针对`go`语言的语法加亮展示）

![](/img/article/chrome/chrome-editor-ext.png)

这不得不让人感慨了。这行代码，还支持Firefox，Safari及高版本的IE。当然，别忘了`CTRL + S` 保存编辑内容。


