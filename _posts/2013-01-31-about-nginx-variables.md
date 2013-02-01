--- 
layout: post
title: nginx变量漫谈
date: 2013-01-31
categories:
  - 技术
tags:
  - nginx
---

![](/img/article/nginx/nginx_lua.jpg)

**本文是之前学习 章亦春 的《nginx教程》的一个粗略笔，记建议去看原稿**

## 安装ngx_openresty

    $ sudo yum install geoip-devel
    $ mkdir -p ~/ngx_openresty && ~/ngx_openresty
    $ http://agentzh.org/misc/nginx/ngx_openresty-1.2.4.3.tar.gz
    $ tar zfvx ngx_openresty-1.2.4.3.tar.gz
    $ cd ngx_openresty-1.2.4.3
    $ wget http://wiki.nginx.org/images/d/d6/Nginx-geoip-0.2.tar.gz
    $ tar xfvz Nginx-geoip-0.2.tar.gz
    $ ls nginx-geoip-0.2
    $ ./configure --with-luajit --prefix=/tmp/ngx_openresty --add-module=./nginx-geoip-0.2
    $ make install


## 变量

Nginx配置文件nginx.conf中，变量采用类似Perl，PHP的标记法。

（1）、需要以$符号作为前缀修饰符，例如$a。

（2）、在变量引用或赋值时，依然需要前缀修饰符$。

（3）、变量赋值用ngx_rewrite模块的set配置指令。
如， 

    set $a "hello world";

（4）、采用$作为修饰，便于基于不同的变量构造新的变量出来。
如，

    set $a hello;
    set $b "$a, $a";

配置范例：

    server {
      listen 8080;
        
      location /test {
        set $foo hello;
        echo "foo: $foo";
        }
    }

测试：（为了便于测试，此处都使用了gnx_echo模块的echo配置指令）

    $ curl 'localhost:80/test'
    foo: Hello, world.

    $ curl -v  'localhost:80/test'
    * About to connect() to localhost port 80 (#0)
    *   Trying 127.0.0.1...
    * connected
    * Connected to localhost (127.0.0.1) port 80 (#0)
    > GET /test HTTP/1.1
    > User-Agent: curl/7.24.0 (x86_64-redhat-linux-gnu) libcurl/7.24.0 NSS/3.13.5.0 zlib/1.2.5 libidn/1.24     libssh2/1.4.1
    > Host: localhost
    > Accept: */*
     > 
    < HTTP/1.1 200 OK
    < Server: ngx_openresty/1.2.4.3
    < Date: Sat, 27 Oct 2012 14:31:13 GMT
    < Content-Type: application/octet-stream
    < Transfer-Encoding: chunked
    < Connection: keep-alive
     <
    foo: Hello, world.
    * Connection #0 to host localhost left intact
    * Closing connection #0

（5）、一个模块是否支持变量插值，由模块的实现来决定。以下采用的gnx_echo模块echo指令，是支持此功能的。

（6）、如果要用echo直接输出含有$字符的字符串，该怎么操作呢？由于到目前为止，没有办法直接将$字符转义掉（nginx还不支持），
所以只能用特殊办法了：

如，用不支持“变量插值”的模块配置指令专门构造取值为$的nginx变量，然后再echo中使用这个变量。如，

    geo $dollar {
      default "$";
     }
     
    server {
     
      listen       80;
      server_name  localhost;
        
      #charset koi8-r;

      location = /test {
        set $foo "Hello, world.";
        echo "foo: $foo";
        echo "This is a dollar sign: $dollar";
        }
     }


    $ curl  'localhost:80/test'
    foo: Hello, world.
    This is a dollar sign: $

（7）在“变量插值”的上下文中,还有一种特殊情况,即当引用的变量名之后紧跟着变量名的构成字符时(比如后跟字母、数字以及下划线),我们就需要使用特别的记法来消除歧义,例如:

    location = /test {
      set $first "Hello ";
      echo "${first}world.";
     }


    $ curl  'localhost:80/test'
    Hello world.

（8）set 指令(以及前面提到的 geo 指令)不仅有赋值的功能,它还有创建 Nginx 变量的副作用,即当作为赋值对象的变量尚不存在时,它会自动创建该变量。比如在上面这个例子中,如果 $a 这个变量尚未创建,则 set 指令会自动创建 $a 这个用户变量。如果我们不创建就直接使用它的值,则会报错。

有趣的是,Nginx 变量的创建和赋值操作发生在全然不同的时间阶段。Nginx 变量的创建只能发生在 Nginx 配置加载的时候,或者说 Nginx 启动的时候;而赋值操作则只会发生在请求实际处理的时候。这意味着不创建而直接使用变量会导致启动失败,同时也意味着我们无法在请求处理时动态地创建新的 Nginx 变量。

Nginx 变量一旦创建,其变量名的可见范围就是整个 Nginx 配置,甚至可以跨越不同虚拟主机的 server 配置块。

从这个例子我们可以看到,set 指令因为是在 location /bar 中使用的,所以赋值操作只会在访问 /bar 的请求中执行。而请求 /foo接口时,我们总是得到空的 $foo 值,因为用户变量未赋值就输出的话,得到的便是空字符串。

从这个例子我们可以窥见的另一个重要特性是,Nginx 变量名的可见范围虽然是整个配置,但每个请求都有所有变量的独立副本,或者说都有各变量用来存放值的容器的独立副本,彼此互不干扰。比如前面我们请求了 /bar 接口后,$foo 变量被赋予了值32,但它丝毫不会影响后续对 /foo 接口的请求所对应的 $foo 值(它仍然是空的!),因为各个请求都有自己独立的 $foo 变量的副本。

对于 Nginx 新手来说,最常见的错误之一,就是将 Nginx 变量理解成某种在请求之间全局共享的东西,或者说“全局变

不是所有的 Nginx 变量都拥有存放值的容器。拥有值容器的变量在 Nginx 核心中被称为“被索引的”(indexed);反之,则被称为“未索引的”(non-indexed)。我们前面在 (二) 中已经知道,像 $arg_XXX 这样具有无数变种的变量群,是“未索引的”。当读取这样的变量时,其实是它的“取处理程序”在起作用,即实时扫描当前请求的 URL 参数串,提取出变量名所指定的 URL 参数的值。很多新手都会对 $arg_XXX 的实
现方式产生误解,以为 Nginx 会事先解析好当前请求的所有 URL参数,并且把相关的 $arg_XXX 变量的值都事先设置好。然而事实并非如此,Nginx 根本不会事先就解析好 URL 参数串,而是在用户读取某个 $arg_XXX 变量时,调用其“取处理程序”,即时去扫描 URL 参数串。类似地,内建变量 $cookie_XXX 也是通过它的“取处理程序”,即时去扫描 Cookie 请求头中的相关定义的。

前面在 (二) 中我们已经了解到变量值容器的生命期是与请求绑定的,但是我当时有意避开了“请求”的正式定义。大家应当一直默认这里的“请求”都是指客户端发起的 HTTP 请求。其实在 Nginx世界里有两种类型的“请求”,一种叫做“主请求”(mainrequest),而另一种则叫做“子请求”(subrequest)。我们先来介绍一下它们。所谓“主请求”,就是由 HTTP 客户端从 Nginx 外部发起的请求。

我们前面见到的所有例子都只涉及到“主请求”,包括 (二) 中那两个使用 echo_exec 和 rewrite 指令发起“内部跳转”的例子。而“子请求”则是由 Nginx 正在处理的请求在 Nginx 内部发起的一种级联请求。“子请求”在外观上很像 HTTP 请求,但实现上却和HTTP 协议乃至网络通信一点儿关系都没有。它是 Nginx 内部的一种抽象调用,目的是为了方便用户把“主请求”的任务分解为多个较小粒度的“内部请求”,并发或串行地访问多个 location 接口,然后由这些 location 接口通力协作,共同完成整个“主请求”。当然,“子请求”的概念是相对的,任何一个“子请求”也可以再发起更多的“子子请求”,甚至可以玩递归调用(即自己调用自己)。

当一个请求发起一个“子请求”的时候,按照 Nginx 的术语,习惯把前者称为后者的“父请求”(parent request)。值得一提的是,Apache服务器中其实也有“子请求”的概念,所以来自 Apache 世界的读者对此应当不会感到陌生。

范例：

    location /main {
      echo_location /foo;
      echo_location /bar;
     }
      
    location /foo {
      echo foo; 
      }
      
     location /bar {
       echo bar; 
      }

测试结果：

    $ curl 'http://localhost:8080/main'
    foo
    bar

但不幸的是,并非所有的内建变量都作用于当前请求。少数内建变量只作用于“主请求”,比如由标准模块 ngx_http_core 提供的内建变量 $request_method。变量 $request_method 在读取时,总是会得到“主请求”的请求方法,比如 GET、POST 之类。我们来测试一下:

    location /main {
      echo "main method: $request_method";
      echo_location /sub;
     }
     
    location /sub {
      echo "sub method: $request_method";
     }

在这个例子里,/main 和 /sub 接口都会分别输出 $request_method 的值。

用curl发起POST类型请求：

    $ curl --data hello 'http://localhost:8080/main'
    main method: POST
    sub method: GET


## 变量执行顺序

    location /test32 {
      echo "sub method: $echo_request_method";
      set $a "hello%20world";
                
      set_unescape_uri $b $a;
      set $c "$b!";
                
      echo $c;
    }


    $ curl 'http://localhost:8080/test32'
    sub method: GET
    hello world!

使用set_by_lua的范例：

    location /test34 {
      set $a 32;
      set $b 200;
      set_by_lua $c "return ngx.var.a + ngx.var.b";
      set $equation "$a + $b = $c";
        
      echo $equation;
     }


    $ curl 'http://localhost:8080/test34'
     32 + 200 = 232

ab测试工具：批量测试

    $ ab -k -c1 -n100000 'http://127.0.0.1:8080/hello'


    location /test {
      # rewrite phase
      set $age 1;
      rewrite_by_lua "ngx.var.age = ngx.var.age + 1";
      # access phase
      deny 10.32.168.49;
      access_by_lua "ngx.var.age = ngx.var.age * 3";
      # content phase
      echo "age = $age";
    }

绝大多数 Nginx 模块在向 content 阶段注册配置指令时,本质上是在当前的 location 配置块中注册所谓的“内容处理程序”(content handler)。每一个 location 只能有一个“内容处理程序”,因此,当在 location 中同时使用多个模块的 content 阶段指令时,只有其中一个模块能成功注册“内容处理程序”。

考虑下面这个有问题的例子:

    ? location /test {
    ?
    echo hello;
    ?
    content_by_lua 'ngx.say("world")';
    ? }

这里, ngx_echo 模块的 echo 指令和 ngx_lua 模块的content_by_lua 指令同处 content 阶段,于是只有其中一个模块能注册和运行这个 location 的“内容处理程序”:

    $ curl 'http://localhost:8080/test'
    world


## Nginx 配置指令的执行顺序(六)

前面我们在 (五) 中提到,在一个 location 中使用 content 阶段指令时,通常情况下就是对应的 Nginx 模块注册该 location 中的“内容处理程序”。那么当一个 location 中未使用任何 content阶段的指令,即没有模块注册“内容处理程序”时,content 阶段会发生什么事情呢?谁又来担负起生成内容和输出响应的重担呢?答案就是那些把当前请求的 URI 映射到文件系统的静态资源服务模块。当存在“内容处理程序”时,这些静态资源服务模块并不会起作用;反之,请求的处理权就会自动落到这些模块上。

前面在 (一) 中提到,Nginx 处理请求的过程一共划分为 11 个阶段,按照执行顺序依次是 post-read、server-rewrite、find-config、rewrite、post-rewrite、preaccess、access、post-access、try-files、content 以及 log.

最先执行的 post-read 阶段在 Nginx 读取并解析完请求头(request headers)之后就立即开始运行。

首先在本地请求一下这个 /test 接口:

    $ curl -H 'X-My-IP: 1.2.3.4' localhost:8080/test
    from: 1.2.3.4

这里使用了 curl 工具的 -H 选项指定了额外的 HTTP 请求头 X-My-IP: 1.2.3.4. 从输出可以看到, $remote_addr 变量的值确实在rewrite 阶段就已经成为了 X-My-IP 请求头中指定的值,即1.2.3.4. 那么 Nginx 究竟是在什么时候改写了当前请求的来源地址呢?答案是:在 post-read 阶段。由于 rewrite 阶段的运行远在 post-read 阶段之后,所以当在 location 配置块中通过 set 配置指令读取 $remote_addr 内建变量时,读出的来源地址已经是经过 post-read 阶段篡改过的。


## 重要模块示例

### 数组处理： array-var-nginx-module 模块

    # enable the ngx_array_var module in your nginx build
    $ ./configure --prefix=/opt/nginx \
    --add-module=/path/to/echo-nginx-module \
    --add-module=/path/to/ngx_devel_kit \
    --add-module=/path/to/set-misc-nginx-module \
    --add-module=/path/to/array-var-nginx-module

     
    location /test_array {
      set $list $1;
    
      array_split ',' $list;
      array_map '[$array_it]' $list;
      array_join ' ' $list;
    
      echo  $list;
    }


### memc缓存

    # enable the ngx_memc module in your nginx build
    $ ./configure --prefix=/opt/nginx \
    --add-module=/path/to/echo-nginx-module \
    --add-module=/path/to/memc-nginx-module


    # (not quite) REST interface to our memcached server
    # at 127.0.0.1:11211

    location = /memc {
      set $memc_cmd $arg_cmd;
      set $memc_key $arg_key;
      set $memc_value $arg_val;
      set $memc_exptime $arg_exptime;
    
      memc_pass 127.0.0.1:11211;
    }

    $ curl 'http://localhost/memc?cmd=flush_all';
    OK
    $ curl 'http://localhost/memc?cmd=replace&key=foo&val=FOO';
    NOT_STORED

    $ curl 'http://localhost/memc?cmd=add&key=foo&val=Bar&exptime=60';
    STORED
    $ curl 'http://localhost/memc?cmd=replace&key=foo&val=Foo';
    STORED
    $ curl 'http://localhost/memc?cmd=set&key=foo&val=Hello';
    STORED

    $ curl 'http://localhost/memc?cmd=get&key=foo';
    Hello
    $ curl 'http://localhost/memc?cmd=delete&key=foo';
    DELETED


### 连接池： keepalive

    # enable Maxim Dounin's ngx_http_upstream_keepalive module
    # in your nginx build
    $ ./configure --prefix=/opt/nginx \
    --add-module=/path/to/echo-nginx-module \
    --add-module=/path/to/memc-nginx-module \
    --add-module=/path/to/ngx_http_upstream_keepalive


    http {
       ...
      upstream my_memc_backend {
      server 127.0.0.1:11211;
      # a connection pool that can cache
      # up to 1024 connections
      keepalive 1024 single;
    }
        
    location = /memc {
        ...
      memc_pass my_memc_backend;
    }


### 子请求捕获：nginx_eval_module

    # enable Valery Kholodkov's nginx_eval_module
    # in your nginx build
    $ ./configure --prefix=/opt/nginx \
    --add-module=/path/to/echo-nginx-module \
    --add-module=/path/to/memc-nginx-module \
    --add-module=/path/to/nginx_eval_module


    location = /save {
      eval_override_content_type 'text/plain';
      eval $res {
      set $memc_cmd 'set';
      set $memc_key $arg_id;
      set $memc_val $arg_name;
      memc_pass 127.0.0.1:11211;
      }
    
     if ($res !~ '^STORED$') {
       return 500;
       break;
      }
     echo 'Done!';
    }


## 相关模块说明

* [ngx_echo](http://wiki.nginx.org/NginxHttpEchoModule): Brings "echo", "sleep", "time","exec", background job and even more shell-style goodies to Nginx config file.
* [ngx_chunkin](http://wiki.nginx.org/NginxHttpChunkinModule): HTTP 1.1 chunked-encoding request body support for Nginx.
* [ngx_headers_more](http://wiki.nginx.org/NginxHttpHeadersMoreModule): Set and clear input and output headers...more than "add"! and even more...
* [ngx_memc](http://wiki.nginx.org/NginxHttpMemcModule): An extended version of the standard memcached module that supports set, add, delete, and many more memcached commands.
* [ngx_drizzle](http://github.com/chaoslawful/drizzle-nginx-module): ngx_drizzlen nginx upstream module that talks to mysql, drizzle, and sqlite3 by libdrizzle.
* [ngx_rds_json](http://github.com/agentzh/rds-json-nginx-module): An nginx output filter that formats Resty DBD Streams generated by ngx_drizzle and others to JSON. Well, still continued...
* [ngx_xss](http://github.com/agentzh/xss-nginx-module): Native support for cross-site scripting (XSS) in an nginx.
* [ngx_set_misc](http://github.com/agentzh/set-misc-nginx-module): Various nginx.conf variable transformation utilities.
* [ngx_array_var](http://github.com/agentzh/array-var-nginx-module): Add support for array variables to nginx config files.


## 扩展阅读

* [agentzh的Nginx教程(版本 2012.09.27)](http://agentzh.org/misc/nginx/agentzh-nginx-tutorials-zhcn.html)
* [OpenResty:一个通过扩展 Nginx实现的强大的 web应用服务器](http://openresty.org/cn/)
* [ngx_lua - Embed the power of Lua into Nginx](http://wiki.nginx.org/HttpLuaModule)
* [Nginx Session 模块](http://chenxiaoyu.org/2011/11/09/nginx-session.html)
* [Nginx-Lua HTTP 401 认证校验](http://chenxiaoyu.org/2012/02/08/nginx-lua-401-auth.html)
* [Nginx 第三方模块试用记](http://chenxiaoyu.org/2011/10/30/nginx-modules.html)
* [lua-resty-beanstalkd 模块教程](http://chenxiaoyu.org/2012/11/25/lua-resty-beanstalkd.html)
* [实现一个简单的服务端推方案](http://huoding.com/2012/09/28/174)
* [Nginx与Lua](http://huoding.com/2012/08/31/156)
* [由Lua 粘合的Nginx生态环境](http://blog.zoomquiet.org/pyblosxom/oss/openresty-intro-2012-03-06-01-13.html)
* [Nginx模块开发入门](http://blog.codinglabs.org/articles/intro-of-nginx-module-development.html)
* [Augmenting APIs with Nginx and Lua](http://3scale.github.com/2013/01/09/augment-your-api-without-touching-it/)


