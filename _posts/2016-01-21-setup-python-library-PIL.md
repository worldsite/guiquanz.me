---
layout: post
title: 通过 PIL 库进行 python 图像处理
date: 2016-01-21
categories:
  - 技术
tags:
  - PIL
---
## 安装 PIL 库

```python

➜  wget http://effbot.org/downloads/Imaging-1.1.7.tar.gz
➜  tar xfvz Imaging-1.1.7.tar.gz
➜  cd Imaging-1.1.7
➜  sudo python setup.py install
```

 或者 sudo pip install PIL --allow-external PIL


```python

➜  pip install --upgrade pip

 ...
_imagingft.c:73:10: fatal error: 'freetype/fterrors.h' file not found
#include <freetype/fterrors.h>
         ^
1 error generated.
error: command 'clang' failed with exit status 1

➜  Imaging-1.1.7  sudo brew install freetype
Warning: freetype-2.6_1 already installed
➜  Imaging-1.1.7  locate freetype/fterrors.h

➜  Imaging-1.1.7  sudo sudo brew install libtiff libjpeg webp little-cms2

➜  Imaging-1.1.7  sudo brew install libtiff libjpeg webp little-cms2
Warning: libtiff-4.0.6 already installed
Warning: jpeg-8d already installed
==> Downloading https://homebrew.bintray.com/bottles/webp-0.4.4.el_capitan.bottle.tar.gz
######################################################################## 100.0%
==> Pouring webp-0.4.4.el_capitan.bottle.tar.gz
🍺  /usr/local/Cellar/webp/0.4.4: 32 files, 1.6M
==> Downloading https://homebrew.bintray.com/bottles/little-cms2-2.7.el_capitan.bottle.tar.gz
######################################################################## 100.0%
==> Pouring little-cms2-2.7.el_capitan.bottle.tar.gz
🍺  /usr/local/Cellar/little-cms2/2.7: 16 files, 1M


➜  Imaging-1.1.7  sudo brew reinstall freetype
==> Reinstalling freetype
==> Downloading https://homebrew.bintray.com/bottles/freetype-2.6_1.el_capitan.bottle.tar.gz
######################################################################## 100.0%
==> Pouring freetype-2.6_1.el_capitan.bottle.tar.gz
🍺  /usr/local/Cellar/freetype/2.6_1: 60 files, 2.5M

➜  Imaging-1.1.7  sudo python setup.py install
running install
running build
running build_py
running build_ext
--- using frameworks at /System/Library/Frameworks
building '_imagingft' extension
clang -fno-strict-aliasing -fno-common -dynamic -I/usr/local/include -I/usr/local/opt/sqlite/include -DNDEBUG -g -fwrapv -O3 -Wall -Wstrict-prototypes -I/usr/local/include/freetype2 -IlibImaging -I/opt/local/include -I/usr/local/Cellar/python/2.7.9/Frameworks/Python.framework/Versions/2.7/include -I/usr/local/include -I/usr/local/Cellar/python/2.7.9/Frameworks/Python.framework/Versions/2.7/include/python2.7 -c _imagingft.c -o build/temp.macosx-10.10-x86_64-2.7/_imagingft.o
_imagingft.c:73:10: fatal error: 'freetype/fterrors.h' file not found
#include <freetype/fterrors.h>
         ^
1 error generated.
error: command 'clang' failed with exit status 1
➜  Imaging-1.1.7
➜  Imaging-1.1.7  ln -s /usr/local/include/freetype2 /usr/local/include/freetype
➜  Imaging-1.1.7  ls /usr/local/Cellar/freetype/
2.6_1
➜  Imaging-1.1.7
➜  Imaging-1.1.7  ls /usr/local/Cellar/freetype/2.6_1
ChangeLog            INSTALL_RECEIPT.json README               bin                  include              lib                  share

➜  Imaging-1.1.7  sudo ln -s /usr/local/include/freetype2 /usr/local/include/freetype

➜  Imaging-1.1.7  sudo python setup.py install
running install
running build
running build_py
running build_ext
...
In file included from _imagingtk.c:19:
/Applications/Xcode.app/Contents/Developer/Platforms/MacOSX.platform/Developer/SDKs/MacOSX10.11.sdk/usr/include/tk.h:78:11: fatal error: 'X11/Xlib.h' file not
      found
#       include <X11/Xlib.h>
                ^
1 error generated.
error: command 'clang' failed with exit status 1

➜  Imaging-1.1.7  locate X11/Xlib.h

/Applications/Xcode.app/Contents/Developer/Platforms/MacOSX.platform/Developer/SDKs/MacOSX10.11.sdk/System/Library/Frameworks/Tk.framework/Versions/8.4/Headers/X11/Xlib.h
/Applications/Xcode.app/Contents/Developer/Platforms/MacOSX.platform/Developer/SDKs/MacOSX10.11.sdk/System/Library/Frameworks/Tk.framework/Versions/8.5/Headers/X11/Xlib.h
/Users/guiq/HackingTeam/hackedteam/core-linux/core/multilib/i386/usr/include/X11/Xlib.h
/Users/guiq/HackingTeam/hackedteam/core-linux/core/multilib/x86_64/usr/include/X11/Xlib.h
/Users/guiq/HackingTeam/hackedteam/rcs-db-ext/Python/tcl/include/X11/Xlib.h
/Users/guiq/HackingTeam/informationextraction/core-linux/core/multilib/i386/usr/include/X11/Xlib.h
/Users/guiq/HackingTeam/informationextraction/core-linux/core/multilib/x86_64/usr/include/X11/Xlib.h
/Users/guiq/HackingTeam/informationextraction/rcs-db-ext/Python/tcl/include/X11/Xlib.h
/Users/guiq/HackingTeam/openjailbreak/absinthe-1/include/wxWidgets-2.8.12/include/wx/x11/nanox/X11/Xlib.h
/Users/guiq/HackingTeam/openjailbreak/absinthe-1/include/wxWidgets-2.9.2/include/wx/x11/nanox/X11/Xlib.h
/Users/guiq/taguba/dato-code/SFrame/cxxtest/test/fake/X11/Xlib.h
/Users/guiq/taguba/dato-code/SFrame/deps/conda/include/X11/Xlib.h
/Users/guiq/taguba/dato-code/SFrame/deps/conda/pkgs/tk-8.5.18-0/include/X11/Xlib.h
/opt/X11/include/X11/Xlib.h

➜  Imaging-1.1.7  ls /usr/local/include/X11
/usr/local/include/X11


➜  Imaging-1.1.7  ls -lart /usr/local/include/X11
lrwxr-xr-x  1 root  wheel  162 10 13 22:59 /usr/local/include/X11 -> /Applications/Xcode.app/Contents/Developer/Platforms/MacOSX.platform/Developer/SDKs/MacOSX10.9.sdk/System/Library/Frameworks/Tk.framework/Versions/8.5/Headers/X11
➜  Imaging-1.1.7

➜  Imaging-1.1.7  sudo rm -rf /usr/local/include/X11
➜  Imaging-1.1.7  sudo ln -s /Applications/Xcode.app/Contents/Developer/Platforms/MacOSX.platform/Developer/SDKs/MacOSX10.11.sdk/System/Library/Frameworks/Tk.framework/Versions/8.4/Headers/X11 /usr/local/include/X11
➜  Imaging-1.1.7  ls /usr/local/include/X11
X.h          Xatom.h      Xfuncproto.h Xlib.h       Xutil.h      cursorfont.h keysym.h     keysymdef.h  xbytes.h
➜  Imaging-1.1.7  sudo python setup.py install
running install
running build
...

running install_egg_info
Writing /usr/local/lib/python2.7/site-packages/PIL/PIL-1.1.7-py2.7.egg-info
creating /usr/local/lib/python2.7/site-packages/PIL.pth
➜  Imaging-1.1.7
```


## 测试验证

```python

➜  python
>>> from PIL import Image
>>> pil_im = Image.open('~/test1.jpg')
>>> pil_im = pil_im.convert('L')
>>> pil_im.save('~/test1.jpg')
>>>
```


## 扩展阅读

* [An introduction to PIL](http://effbot.org/imagingbook/introduction.htm)


## 祝大家玩的开心

## 编程之道，就在[编程之美]

![编程之美](/img/weixin_qr.jpg)

