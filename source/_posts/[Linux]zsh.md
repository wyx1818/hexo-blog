---
title: Linux | zsh+on-my-zsh 配置
tags: Linux
categories: Linux
abbrlink: zsh
date: 2019-04-27 14:01:30
---

<style>.post-copyright{display:none}</style>

本文以 CentOS 7/Mac 为例，介绍 zsh 的配置使用教程。

## 准备

### 查看当前环境 shell

```
echo $SHELL
```

<!-- more -->

### 查看系统自带哪些 shell

```
cat /etc/shells
```

## 安装 zsh

```
yum install zsh # CentOS
brew install zsh # mac安装
```

### 将`zsh`设置为默认 shell

```
chsh -s /bin/zsh # CentOS
# Mac如下
# 在 /etc/shells 文件中加入如下一行
/usr/local/bin/zsh
# 接着运行
chsh -s /usr/local/bin/zsh
```

可以通过`echo $SHELL`查看当前默认的 shell，如果没有改为`/bin/zsh`，那么需要重启 shell。

## oh-my-zsh

配置 zsh 是一件麻烦的事儿，爱折腾的程序猿怎么可能忍受？！于是，[oh-my-zsh](https://github.com/robbyrussell/oh-my-zsh)出现了，有了这个东东，zsh 配置起来就方便多了！

### 安装 oh-my-zsh

有若干安装方式，介绍三种： 1.自动安装

```
wget https://github.com/robbyrussell/oh-my-zsh/raw/master/tools/install.sh -O - | sh
```

2.手动安装

```
git clone git://github.com/robbyrussell/oh-my-zsh.git ~/.oh-my-zsh
cp ~/.oh-my-zsh/templates/zshrc.zsh-template ~/.zshrc
```

3.真-手动安装

- 在[oh-my-zsh](https://github.com/robbyrussell/oh-my-zsh)的 github 主页，手动将 zip 包下载下来。
- 将 zip 包解压，拷贝至`~/.oh-my-zsh`目录。此处省略拷贝的操作步骤。
- 执行`cp ~/.oh-my-zsh/templates/zshrc.zsh-template ~/.zshrc`

三选一即可，适合各种环境下的安装，然后需要`source ~/.zshrc`将配置生效。以下修改了`.zshrc`文件之后，都执行一下这个命令。

### zsh 主题

通过如下命令可以查看可用的`Theme`：

```
# ls ~/.oh-my-zsh/themes
```

如何修改 zsh 主题呢？
编辑`~/.zshrc`文件，将`ZSH_THEME="candy"`,即将主题修改为`candy`。我采用的`steeef`。

### zsh 扩展

在`~/.zshrc`中找到`plugins`关键字，就可以自定义启用的插件了，系统默认加载`git`。

#### git 插件

命令内容可以参考`cat ~/.oh-my-zsh/plugins/git/git.plugin.zsh`。

常用的：

```
gapa    git add --patch
gc!    git commit -v --amend
gcl    git clone --recursive
gclean    git reset --hard && git clean -dfx
gcm    git checkout master
gcmsg    git commit -m
gco    git checkout
gd    git diff
gdca    git diff --cached
gp    git push
grbc    git rebase --continue
gst    git status
gup    git pull --rebase
```

完整列表：`https://github.com/robbyrussell/oh-my-zsh/wiki/Plugin:git`

### extract

解压文件用的，所有的压缩文件，都可以直接`x filename`，不用记忆参数

当然，如果你想要用`tar`命令，可以使用`tar -`加`tab`键，zsh 会列出参数的含义。

#### autojump

按照[官方文档](https://github.com/wting/autojump)介绍，需要使用如下命令安装，而不是一些博客中的介绍：

```
yum install autojump-zsh # CentOS
brew install autojump # Mac
```

`CentOS`安装好之后，需要在`~/.zshrc`中配置一下，除了在`plugins`中增加`autojump`之外，还需要添加一行：

```
[[ -s ~/.autojump/etc/profile.d/autojump.sh ]] && . ~/.autojump/etc/profile.d/autojump.sh
```

安装好之后，记得`source ~/.zshrc`，然后你就可以通过`j+目录名`快速进行目录跳转。支持目录名的模糊匹配和自动补全。

- `j -stat`：可以查看历史路径库

#### zsh-autosuggestions

[zsh-autosuggestions](https://github.com/zsh-users/zsh-autosuggestions)

```
git clone git://github.com/zsh-users/zsh-autosuggestions $ZSH_CUSTOM/plugins/zsh-autosuggestions
```

在 `~/.zshrc` 中配置

```
plugins=(其他的插件 zsh-autosuggestions)
```

因为箭头`→`不太方便，在`.zshrc`中自定义补全快捷键为逗号，但是又一次遇到了需要输入逗号的情况，所以，并不太推荐如下修改：

```
bindkey ',' autosuggest-accept
```

#### zsh-syntax-highlighting

[zsh-syntax-highlighting](https://github.com/zsh-users/zsh-syntax-highlighting)

```
git clone https://github.com/zsh-users/zsh-syntax-highlighting.git ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-syntax-highlighting
```

`~/.zshrc`文件中配置：

```
plugins=(其他的插件 zsh-syntax-highlighting)
```

#### git-open

[git-open](https://github.com/paulirish/git-open)插件可以在你 git 项目下打开远程仓库浏览项目。

```
git clone https://github.com/paulirish/git-open.git $ZSH_CUSTOM/plugins/git-open
```

#### bat

`bat` 代替 `cat`
`cat` 某个文件，可以在终端直接输出文件内容，`bat` 相比 `cat` 增加了行号和颜色高亮 👍

```
brew install bat
```

## 常用快捷键

- 命令历史记录
  - 一旦在 shell 敲入正确命令并能执行后，shell 就会存储你所敲入命令的历史记录（存放在`~/.zsh_history` 文件中），方便再次运行之前的命令。可以按方向键 ↑ 和 ↓ 来查看之前执行过的命令
  - 可以用 `r`来执行上一条命令
  - 使用 `ctrl-r` 来搜索命令历史记录
- 命令别名
  - 可以简化命令输入，在 `.zshrc` 中添加 `alias shortcut='this is the origin command'` 一行就相当于添加了别名
  - 在命令行中输入 `alias` 可以查看所有的命令别名

## 使用技巧

- 连按两次 Tab 会列出所有的补全列表并直接开始选择，补全项可以使用 ctrl+n/p/f/b 上下左右切换
- 智能跳转，安装了 autojump 之后，zsh 会自动记录你访问过的目录，通过 j 目录名 可以直接进行目录跳转，而且目录名支持模糊匹配和自动补全，例如你访问过 hadoop-1.0.0 目录，输入 j hado 即可正确跳转。j --stat 可以看你的历史路径库。
- 命令选项补全。在 zsh 中只需要键入 tar -<tab> 就会列出所有的选项和帮助说明
- 在当前目录下输入 .. 或 ... ，或直接输入当前目录名都可以跳转，你甚至不再需要输入 `cd` 命令了。在你知道路径的情况下，比如 `/usr/local/bin` 你可以输入` cd /u/l/b` 然后按进行补全快速输入
- 目录浏览和跳转：输入 d，即可列出你在这个会话里访问的目录列表，输入列表前的序号，即可直接跳转。
- 命令参数补全。键入` kill <tab>` 就会列出所有的进程名和对应的进程号
- 更智能的历史命令。在用或者方向上键查找历史命令时，zsh 支持限制查找。比如，输入 ls,然后再按方向上键，则只会查找用过的 ls 命令。而此时使用则会仍然按之前的方式查找，忽略 ls
- 多个终端会话共享历史记录
- 通配符搜索：`ls -l **/*.sh`，可以递归显示当前目录下的 shell 文件，文件少时可以代替 `find`。使用 `**/` 来递归搜索
- 扩展环境变量，输入环境变量然后按 就可以转换成表达的值
- 在 .zshrc 中添加 `setopt HIST_IGNORE_DUPS` 可以消除重复记录，也可以利用`sort -t ";" -k 2 -u ~/.zsh_history | sort -o ~/.zsh_history`手动清除

## 参考

- [wting/autojump--官方文档](https://github.com/wting/autojump)
- [powerline/fonts](https://github.com/powerline/fonts)

### Linux

- [终极 Shell](http://macshuo.com/?p=676)
- [Ubuntu 16.04 下安装 zsh 和 oh-my-zsh](https://www.cnblogs.com/EasonJim/p/7863099.html)
- [Ubuntu 下安装 oh-my-zsh](https://www.jianshu.com/p/9a5c4cb0452d)
- [掘金-Shell 中的极品-- Zsh](https://juejin.im/entry/5b46b268f265da0f793a5ae1)
- [CentOS 7 下 autojump 无法使用的可能原因](http://blog.csdn.net/huangbo10/article/details/50930002)
- [oh-my-zsh 配置你的 zsh 提高 shell 逼格终极选择](http://yijiebuyi.com/blog/b9b5e1ebb719f22475c38c4819ab8151.html)

### Mac

- [zsh oh-my-zsh 插件推荐](https://hufangyun.com/2017/zsh-plugin/)
- [zsh 全程指南-推荐](http://wdxtub.com/2016/02/18/oh-my-zsh/)
- [iterm 主题下载](https://iterm2colorschemes.com/)
- [程序员内功系列--iTerm 与 Zsh 篇](https://xiaozhou.net/learn-the-command-line-iterm-and-zsh-2017-06-23.html)
- [Mac 下配置终端环境 iTerm2 + Zsh + Oh My Zsh + tmux](https://www.dreamxu.com/mac-terminal/)

本文转自：[zsh+on-my-zsh 配置教程指南（程序员必备）【已备份】](https://segmentfault.com/a/1190000013612471)
