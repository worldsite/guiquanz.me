---
layout: post
title: Lua FFI 实战
date: 2013-05-19
categories:
  - 技术
tags:
  - lua
  - ffi
---
## 由来

[FFI](http://luajit.org/ext_ffi.html)库，是[LuaJIT](http://luajit.org)中最重要的一个扩展库。它允许从纯Lua代码调用外部C函数，使用C数据结构。有了它，就不用再像`Lua`标准[math](https://github.com/guiquanz/skynet_with_lua-5.2.2/blob/master/deps/lua-5.2.2/src/lmathlib.c)库一样，编写Lua扩展库。把开发者从开发Lua扩展C库（语言/功能绑定库）的繁重工作中释放出来。

## FFI简介

FFI库，允许从纯Lua代码调用外部C函数，使用C数据结构。

FFI库最大限度的省去了使用C手工编写繁重的Lua/C绑定的需要。不需要学习一门独立/额外的绑定语言——它`解析普通C声明`。这样可以从C头文件或参考手册中，直接`剪切，粘贴`。它的任务就是`绑定很大的库，但不需要捣鼓脆弱的绑定生成器`。

FFI紧紧的整合进了LuaJIT（几乎不可能作为一个独立的模块）。JIT编译器为Lua代码直接访问C数据结构而产生的代码，等同于一个C编译器应该生产的代码。在JIT编译过的代码中，调用C函数，可以被`内连`处理，不同于基于Lua/C API函数调用。

这一页将简要介绍FFI库的使用方法。

## 激励范例：调用外部C函数

真的很用容易去调用一个外部C库函数：
     
    ① local ffi = require("ffi") 
    ② ffi.cdef[[
      int printf(const char* fmt, ...);
        ]]
    ③ ffi.C.printf("Hello %s!", "world")
     
以上操作步骤，如下：

    ① 加载FFI库
    ② 为函数增加一个函数声明。这个包含在`中括号`对之间的部分，是标准C语法。.
    ③ 调用命名的C函数——非常简单


事实上，背后的实现远非如此简单：③ 使用标准C库的命名空间`ffi.C`。通过符号名("printf")索引这个命名空间，自动绑定标准C库。索引结果是一个特殊类型的对象，当被调用时，执行`printf`函数。传递给这个函数的参数，从Lua对象自动转换为相应的C类型。

Ok，使用`printf()`不是一个壮观的示例。你也可能使用了`io.write()`和`string.format()`。但你有这个想法……
以下是一个Windows平台弹出消息框的示例：

<pre class="prettyprint linenums">
local ffi = require("ffi")
ffi.cdef[[
int MessageBoxA(void *w, const char *txt, const char *cap, int type);
]]
ffi.C.MessageBoxA(nil, "Hello world!", "Test", 0)
</pre>

Bing! 再一次, 远非如此简单，不?

和要求使用Lua/C API去绑定函数的努力相比：

* 创建一个外部C文件，
* 增加一个C函数，遍历和检查Lua传递的参数，并调用这个真实的函数，

__传统的处理方式__

* 增加一个模块函数列表和对应的名字，
* 增加一个luaopen_*函数，并注册所有模块函数，
* 编译并链接为一个动态库（DLL），
* 并将库文件迁移到正确的路径，
* 编写Lua代码，加载模块
* 等等……
* 最后调用绑定函数。

唷！（很不爽呀！）


## 激励示例: 使用C数据结构

FFI库允许你创建，并访问C数据结构。当然，其主要应用是C函数接口。但，也可以独立使用。

Lua构建在高级数据类型之上。它们很灵活、可扩展，而且是动态的。这就是我们大家都喜欢Lua的原因所在。唉，针对特殊任务，你需要一个低级的数据结构时，这可能会低效。例如，一个超大的不同结构的数组，需要通过一张超大的表，存储非常多的小表来实现。这需要大量的内存开销以及性能开销。

这里是一个库的草图，操作一个彩图，以及一个基准。首先，朴素的Lua版本，如下：

<pre class="prettyprint linenums">
local floor = math.floor

local function image_ramp_green(n)
  local img = {}
  local f = 255/(n-1)
  for i=1,n do
    img[i] = { red = 0, green = floor((i-1)*f), blue = 0, alpha = 255 }
  end
  return img
end

local function image_to_grey(img, n)
  for i=1,n do
    local y = floor(0.3*img[i].red + 0.59*img[i].green + 0.11*img[i].blue)
    img[i].red = y; img[i].green = y; img[i].blue = y
  end
end

local N = 400*400
local img = image_ramp_green(N)
for i=1,1000 do
  image_to_grey(img, N)
end
</pre>

以上代码，创建一个160.000像素的一张表，其中每个元素是一张持有4个范围0至255的数字值的表。首先，创建了一张绿色斜坡的图（1D，为了简单化），然后进行1000次灰阶转换操作。实在很蠢蛋，可是我需要一个简单示例……

以下是FFI版本代码。其中，被修改的部分加粗标注：

<pre>
① local ffi = require("ffi")
ffi.cdef[[
typedef struct { uint8_t red, green, blue, alpha; } rgba_pixel;
]]

② local function image_ramp_green(n)
  local img = ffi.new("rgba_pixel[?]", n)
  local f = 255/(n-1)
③  for i=0,n-1 do
④  img[i].green = i*f
    img[i].alpha = 255
  end
  return img
end

local function image_to_grey(img, n)
③ for i=0,n-1 do
⑤   local y = 0.3*img[i].red + 0.59*img[i].green + 0.11*img[i].blue
    img[i].red = y; img[i].green = y; img[i].blue = y
  end
end

local N = 400*400
local img = image_ramp_green(N)
for i=1,1000 do
  image_to_grey(img, N)
end
</pre>

Ok, 这是不是太困难:

① 首先，加载FFI库，声明底层数据类型。这里我们选择一个数据结构，持有4字节字段，每一个由`4x8 RGBA`像素组成。

② 通过`ffi.new()`直接创建这个数据结构——其中'?'是一个占位符，`变长数组元素个数`。

③ C数据是基于0的（zero-based），所以索引必须是0 到 n-1。你可能需要分配更多的元素，而不仅简化转换一流代码。

④ 由于`ffi.new()`默认`0填充`（zero-fills）数组, 我们仅需要设置绿色和alpha字段。

⑤ 调用`math.floor()`的过程可以省略，因为转换为整数时，浮点数已经被向0截断。这个过程隐式的发生在数据被存储在每一个像素的字段时。

现在让我们看一下主要影响的变更：

首先，内存消耗从22M降到640K(400*400*4字节)。少了`35x`。所以，表确实有一个显著的开销。BTW（By the Way: 顺便说一句）: 原始Lua程序在x64平台应该消耗40M内存。

其次，性能：纯Lua版本运行耗时9.57秒（使用Lua解析器52.9秒），而FFI版本在我的主机上耗时0.48秒（YMMV: 因人而异）。快了`20x`（比Lua解析器快了110x`）。 

狂热的读者，可能注意到了为颜色将纯Lua代码版本转为使用数组索引（[1] 替换 .red, [2] 替换 .green 等）应该更加紧凑和更快。这个千真万确（大约`1.7x`）。从结构切换到数组也会有帮助。

虽然最终的代码不是惯用的，而容易出错。它仍然没有得到甚至接近FFI版本代码的性能。同时，高级数据结构不容易传递给别的C函数，尤其是I/O函数，没有过分转换处罚。

~~待续~~


## 扩展阅读

* [LuaJit FFI Library](http://luajit.org/ext_ffi.html)
* [Terra](http://terralang.org/getting-started.html)
* [LPEG: Parsing Expression Grammars For Lua, version 0.12](http://www.inf.puc-rio.br/~roberto/lpeg/)
* [Lua中通过ffi调用c的结构体变量](http://seagg.github.io/blog/2012/12/24/lua-struct/)
* [使用 luajit 的 ffi 绑定 zeromq](http://blog.codingnow.com/2011/06/luajit_ffi_zeromq.html)
* [Playing with LuaJIT FFI](http://develcuy.com/en/playing-luajit-ffi)
* [LuaJIT FFI 调用 Curl 示例](http://chenxiaoyu.org/2012/10/11/luajit-ffi-curl-example.html)
* [Lua String Templates](https://github.com/weshoke/Lust)
* [Standalone FFI library for calling C functions from lua](https://github.com/jmckaskill/luaffi)
* [Lua游戏开发实践指南](http://product.china-pub.com/3020698)
* [	Lua程序设计:第2版](http://product.china-pub.com/40562)
* [	Beginning Lua Programming](http://product.china-pub.com/2021989)


## 安装LuaJIT

    mkdir -p ~/lua-ffi_in_action && cd ~/lua-ffi_in_action
    git clone http://luajit.org/git/luajit-2.0.git
    cd luajit-2.0
    make && make install


## 祝大家玩的开心

