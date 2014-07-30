---
layout: post
title: Openssl的危机和分化
date: 2014-04-27
categories:
  - 技术
tags:
  - openssl
  - 缓冲区溢出
---

## 引爆点

![](/img/article/2014-04/27-04.jpg)

2014年4月7日，知名开源安全软件`OpenSSL`，被一家名为[codenomicon](http://www.codenomicon.com/)的公司爆出[CVE-2014-0160](http://heartbleed.com/)`心脏出血`漏洞（有位记者还专门为bug发现过程发了一篇文章，[How Codenomicon Found The Heartbleed Bug Now Plaguing The Internet](http://readwrite.com/2014/04/13/heartbleed-security-codenomicon-discovery#awesm=~oCEIp2QsAqbTzo)不烦一看）。该bug允许攻击者读取存在bug的系统的64kb处理内存，暴露加密流的密钥、用户名以及访问的内容等。由于OpenSSL是标配的安全套件之一，不光互联网系统（Apache、Nginx等），很多嵌入式设备（包括Android系统等），都默认使用它。所以，这是一个非常严重的漏洞，有一种悲观的估算，大概有三分之二的互联网系统受此影响。之后，又相续爆出另外一些漏洞，这直接导致了OpenSSL的分化。

## OpenSSL Heartbleed的本质

如果你幸好看了[http://git.openssl.org](http://git.openssl.org/gitweb/?p=openssl.git;a=commitdiff;h=96db9023b881d7cd9f379b0c154650d6c108e9a3)上bug修复的代码变更，就会知道这是一个非常低级的bug，我甚至非常怀疑OpenSSL的这些哥们是怎么做`代码评审`的（是过场子吗，还是直接都不审啦？）。如此知名的一个软件，竟然犯如此低级的一个错误（就像`苹果公司`的那个`goto fail`[Apple’s SSL/TLS bug](https://www.imperialviolet.org/2014/02/22/applebug.html)（欲知详情，请阅读`酷壳`上的文章[由苹果的低级Bug想到的](http://coolshell.cn/articles/11112.html)）一样威武），而且是在事发2年之后，才被人发现。这太可怕了，所幸还是被揪出来了。这绝对是开源界的一个大耻辱。

[![](/img/article/2014-04/27-05.png)](http://git.openssl.org/gitweb/?p=openssl.git;a=commitdiff;h=96db9023b881d7cd9f379b0c154650d6c108e9a3))

明白人一看便知晓：`这是一个标准的缓冲区溢出漏洞，错就错在没有对待填充内容长度的有效性进行验证`。当填充长度大于实际内容的长度时，就会把后面的内容爆出来。非常感谢幽默的`xkcd`君为大家献出了一组有趣的漫画，非常清晰的描述了其背后的原理。

[![xkcd](/img/article/2014-04/27-02.jpg)](http://xkcd.com/1354/)


## OpenSSL存在的问题

OpenSSL是默认的安全套件，存在于各种电子设备中，相信很多人都没有仔细研究过。按理说，像这样复杂而重要的组件，应该有完备的文档以及大量的测试代码作为QA的强大后盾，但实际上少的可怜，像样的一个开发文档都没有……种种情景，不可思议。大家都是集体无意识了，想当然的去用了，不问为什么，也不去探究……，出问题时都瞎了。当然，另一个原因，那就是OpenSSL的代码太庞大、复杂，凌乱了，不易于普通开发者学习和使用……。项目资金少（据说一年才$2000多点），没有足够的人力去维护和优化，最基本的变更评审都没有保障了。其实，这也是很多开源项目的现状。希望未来会有所改观。


## 业界的动态

OpenSSL的问题爆出来之后，包括 Google、微软、Facebook 在内的十二家科技巨头就发起了一个叫做 `the Core Infrastructure Initiative`的项目。作为出资方，Google 等巨头每年的出资额都在 10 万美元以上，期限三年，总共项目经费为 320 万美元。经费由 Linux Foundation 管理。经费的主要目的是保证软件基础设施安全，其中 OpenSSL 自然是主要对象，另外，ModSSL、PGP 、OpenCryptolab 等基础协议也在受保护范围内。

该项目执行理事 Jim Zemlin 称：“这其实是我们很早之前就应该做的事了。当然，如果我们五年后再来看，我们也可能会意识到今天做的事是多么重要！”（锤子科技，老罗的那个100万人民币我就不八卦了）。但愿OpenSSL的明天更美好。


## OpenSSL的分化

以此同时，[OpenBSD](http://www.openbsd.org/cgi-bin/cvsweb/src/lib/libssl/)社区也忍不住了，终于fork出了`libressl`分支[http://www.libressl.org/](http://www.libressl.org)，决心不让自己的用户心脏流血了。将去除对32主机及Windows平台的支持，同时分离出`libcrypto`和`libssl`两个程序库。目前已经发布[2.0.3](http://ftp.openbsd.org/pub/OpenBSD/LibreSSL/libressl-2.0.3.tar.gz)版本了，有兴趣的同学可以去尝鲜了。当然，你也可以了解一下这背后的故事和八卦、吐嘈[LibreSSL - An OpenSSL replancement. The first 30 days, and where we go from here](http://www.openbsd.org/papers/bsdcan14-libressl/mgp00001.html)。

谷歌也创建了OpenSSL的分支[boringssl](https://boringssl.googlesource.com/boringssl)，但使用BoringSSL的代码不能保证API或ABI的稳定性，所以谷歌会继续向 OpenSSL递交bug修正，继续资助Core Infrastructure Initiative和OpenBSD基金会。

__Boringssl的背景：谷歌使用了超过70个OpenSSL补丁，部分被接受合并到了OpenSSL主库，但大部分没有。随着Android、Chrome和其它项目开始需要这些补丁的子集，事情日益变得复杂，要保证所有补丁在不同代码库正常工作需要太多精力。所以他们决定创建OpenSSL分支。__

这无疑出现了OpenSSL的分化，但这是件好事。证明大家开始在关注OpenSSL，关注开源软件质量问题等，另外，给用户多一个选择未尝不是件好事。


## 未来展望

## 相关开源项目

* [https://github.com/openssl/openssl](https://github.com/openssl/openssl)
* [https://github.com/polarssl/polarssl](https://github.com/polarssl/polarssl)
* [https://polarssl.org](https://polarssl.org/)
* [https://github.com/cyassl/cyassl](https://github.com/cyassl/cyassl)
* [http://www.cryptol.net/](http://www.cryptol.net)
* [http://www.libressl.org/](http://www.libressl.org)
* [AppCheck for Heartbleed vulnerability](http://appcheck.codenomicon.com/)
* [CSHeartbleedScanner](http://download.crowdstrike.com/heartbleed/CSHeartbleedScanner.zip)
* [boringssl](https://boringssl.googlesource.com/boringssl)
* [LibreSSL](http://www.libressl.org/)


## 扩展阅读

* [The Heartbleed Bug](http://heartbleed.com/)
* [加密算法编写工具Cryptol DSL目前已开源](http://code.csdn.net/news/2819505) 
* [OpenSSL漏洞相关报道](http://www.oschina.net/search?q=OpenSSL&scope=news&days=0&onlytitle=1&sort_by_time=1)
* [Diagnosis of the OpenSSL Heartbleed Bug](http://blog.existentialize.com/diagnosis-of-the-openssl-heartbleed-bug.html)
* [wuyun知识库: 关于OpenSSL“心脏出血”漏洞的分析](http://drops.wooyun.org/papers/1381)
* [CyaSSL: 轻量的SSL库](http://segmentfault.com/a/1190000000471532)
* [PolarSSL: 在乎代码可读性的开源SSL库](http://segmentfault.com/a/1190000000472940)
* [漫画解释 openSSL 的「heartbleed」漏洞](http://jandan.net/2014/04/12/openssl-heartbleed.html)
* [Google、微软、Facebook联合发布320万美元项目，阻止下一个Heartbleed漏洞](http://www.36kr.com/p/211477.html)
* [CROWDSTRIKE HEARTBLEED SCANNER Network Scan for OpenSSL Vulnerability](http://www.crowdstrike.com/community-tools/index.html)
* [Comparison of TLS implementations](http://en.wikipedia.org/wiki/Comparison_of_TLS_implementations)
* [Heartbleed 漏洞万能扫描工具出炉](http://www.oschina.net/news/51210/crowdstrike-heartbleed-scanner)
* [由苹果的低级Bug想到的](http://coolshell.cn/articles/11112.html)
* [Google的OpenSSL 分支 BoringSSL](https://boringssl.googlesource.com/boringssl)


## 祝大家玩的开心

## 编程之道，就在[编程之美]

![编程之美](/img/weixin_qr.jpg)

