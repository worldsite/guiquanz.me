---
layout: post
title: 基于 iTerm2 和 zsh 等的终端配置
date: 2015-11-01
categories:
  - 技术
tags:
  - iTerm2
---
## 缘由

Mac OS X 默认的终端，使用 bash，但不是特别好用。推荐使用 [zsh](http://www.zsh.org/)，但是 zsh 配置实在繁琐，用的人很少，直到 [oh-my-zsh](http://ohmyz.sh/) 的出现，才改变了这个现状（当然，你也可以尝试 [grmlzshrc](http://grml.org/zsh/#grmlzshrc)）。以下就介绍基于 [iTerm2](http://iterm2.com/) 的 终端配置过程和注意事项。


## 安装 iTerm2

Mac OS X 自带的 终端，尚能使用，但已明显落后这个时代了，建议使用 [iterm2](http://iterm2.com/downloads.html)。到官网下载安装包，并按照提示安装。


## 选择色彩主体

iTerm2 有很多色彩配置方案，可以参考 [https://github.com/mbadolato/iTerm2-Color-Schemes](ttps://github.com/mbadolato/iTerm2-Color-Schemes) 进行挑选和应用。


## 安装 zsh

```shell

$ sudo brew install zsh
```

### 配置 zsh 为默认 shell

```shell

$ sudo vim /etc/shells

# 追加

/usr/local/bin/zsh

# 保存，然后退出

# 修改 sh
$ chsh -s /usr/local/bin/zsh

```

### 合并配置

由于修改 Shell 之后，使用的是 `~/.zshrc` 配置文件，如果之前使用的是 `~/.profile` 或 `~/.bash_profile` ，那么需要将其中的内容同步到 `~/.zshrc` 中，否则
每次打开终端都需要单独执行应用配置的命令，那会添加不少麻烦。

然后，需要执行以下命令，启用新配置：

```shell

$ source ~/.zshrc

```

## 安装 oh-my-zsh

```shell

$ wget https://github.com/robbyrussell/oh-my-zsh/raw/master/tools/install.sh -O - |sh
```

或者，手动安装：

```bash

$ git clone git://github.com/robbyrussell/oh-my-zsh.git ~/.oh-my-zsh
$ cp ~/.oh-my-zsh/templates/zshrc.zsh-template ~/.zshrc
```

### 配置主体

`oh-my-zsh` 有非常多的主体可以选择，具体详情请阅读 [https://github.com/robbyrussell/oh-my-zsh/wiki/Themes](https://github.com/robbyrussell/oh-my-zsh/wiki/Themes)，选择你喜欢的便是了。然后修改配置：(假设我们现在了 `robbyrussell` 这个主体)

```shell

$ vim ~/.zshrc

ZSH_THEME="robbyrussell"

# 保存，然后退出

$ source ~/.zshrc

```


### 主要配置项说明

```text

export ZSH=/Users/todo/.oh-my-zsh

ZSH_THEME="agnoster"

DEFAULT_USER="todo"

plugins=(git brew node npm)

source $ZSH/oh-my-zsh.sh
```

* `ZSH` 变量，指定 oh-my-zsh 的主目录；
* `ZSH_THEME` 变量，指定 oh-my-zsh 的主体；
* `DEFAULT_USER`, 指定你的用户名，用于__隐藏命令行前面的用户__，便于在公开场合使用；
* `plugins` 是需要加载的各种插件；
* 最后，`source` 命令把上述配置好的变量一并带到 oh-my-zsh 的脚本里执行，从而达到配置zsh的目的。


### 插件配置

`oh-my-zsh` 自带了很多插件，但默认仅配了 git 一个。所有的插件都在 `~/.oh-my-zsh/plugins/` 目录下，可以根据实际需要进行配置。打开 ~/.zshrc 文件，找到 `plugins=(git` 位置，然后在括号内增加你选的插件列表，最后让配置生效。具体操作，如下：

```shell

$ vim ~/.zshrc

plugins=(git ruby gem osx autojump go man brew github npm man grunt tmux node)

$ source ~/.zshrc
```

### 安装 autojump

由于我们启用了 autojump 插件，所以需要安装 [autojump - https://github.com/wting/autojump](https://github.com/wting/autojump) 工具。具体操作，如下：

```shell

$ sudo brew install autojump
``` 


## PowerLine 字体

如果你使用了 agnoster 配置主体，那么需要 [PowerLine - https://github.com/powerline/fonts](https://github.com/powerline/fonts) 字体，可以根据源码进行安装，或者[下载打过补丁的版本](https://github.com/Lokaltog/powerline-fonts/blob/master/Meslo/Meslo%20LG%20M%20DZ%20Regular%20for%20Powerline.otf) 进行安装。 然后，打开 iTerm2 修改字体设置。界面操作流程，如下：

```shell

iTerm -> preferences -> profiles -> text
```

## 自动补全

```text

# completion
autoload -U compinit
compinit

# correction
setopt correctall

# prompt
autoload -U promptinit
promptinit
```

### compdef 相关问题解决

如果打开终端时，出现类似 `compdef: unknown command or service: git` 错误，可以通过以下方式解决：

```shell

$ compaudit |sudo xargs chmod g-w
$ compaudit |sudo xargs chown root
$ rm ~/.zcompdump*
$ compinit
```

## 快捷键

```text

⌘ + Click：可以打开文件，文件夹和链接
⌘ + n：新建窗口
⌘ + t：新建标签页
⌘ + w：关闭当前页
⌘ + 数字 & ⌘ + 方向键：切换标签页
⌥⌘ + 数字：切换窗口
⌘ + enter：切换全屏
⌘ + d：左右分屏
⇧⌘ + d：上下分屏
⌘ + ;：自动补全历史记录
⇧⌘ + h：自动补全剪贴板历史
⌥⌘ + e：查找所有来定位某个标签页
⌘ + r & ⌃ + l：清屏
⌘ + /：显示光标位置
⌥⌘ + b：历史回放
⌘ + f：查找，然后用 tab 和 ⇧ + tab 可以向右和向左补全，补全之后的内容会被自动复制， 还可以用 ⌥ + enter 将查找结果输入终端

选中即复制，鼠标中键粘贴

很多快捷键都是通用的，和 Emace 等都是一样的

⌃ + u：清空当前行
⌃ + a：移动到行首
⌃ + e：移动到行尾
⌃ + f：向前移动
⌃ + b：向后移动
⌃ + p：上一条命令
⌃ + n：下一条命令
⌃ + r：搜索历史命令
⌃ + y：召回最近用命令删除的文字
⌃ + h：删除光标之前的字符
⌃ + d：删除光标所指的字符
⌃ + w：删除光标之前的单词
⌃ + k：删除从光标到行尾的内容
⌃ + t：交换光标和之前的字符
```


## 安装 tmux

[tmux](http://tmux.github.io)，是一个终端复用软件，可将终端方案化。当需要远程访问很多系统时，tmux 就派上用场了。安装方式，如下：

```shell

$ sudo brew install tmux
```

### 配置

$ vim ~/.tmux.conf


## Mosh

在网络不好的环境下，可以尝试使用 [Mosh](https://mosh.mit.edu/) 来代替 SSH 工具。

```shell

 $ sudo brew install mobile-shell
```


## 扩展阅读

* [`iTerm2` is a replacement for Terminal and the successor to iTerm](http://iterm2.com/index.html)
* [oh-my-zsh on www.github.com](https://github.com/robbyrussell/oh-my-zsh)
* [oh-my-zsh theme web site](http://zshthem.es/all/) 
* [oh-my-zsh 主题效果及说明](https://github.com/robbyrussell/oh-my-zsh/wiki/Themes)
* [如何配置一个高效的 Mac 工作环境](https://github.com/macdao/ocds-guide-to-setting-up-mac)
* [`fishshell` - Finally, a command line shell for the 90s](http://fishshell.com/)
* [我在用的mac软件(1)--终端环境之iTerm2](http://foocoder.com/blog/wo-zai-yong-de-macruan-jian.html/)
* [Mac 键盘快捷键](https://support.apple.com/zh-cn/HT201236)
* [Mac 键盘的辅助功能快捷键](https://support.apple.com/zh-cn/HT204434)
* [Mosh - Remote terminal application that allows roaming](https://mosh.mit.edu/)
* [程序员如何优雅地使用 Mac？](http://www.zhihu.com/question/20873070)
* [SOLARIZED - Precision colors for machines and people](http://ethanschoonover.com/solarized)


## 祝大家玩的开心

## 编程之道，就在[编程之美]

![编程之美](/img/weixin_qr.jpg)

