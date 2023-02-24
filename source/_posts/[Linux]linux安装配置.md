---
title: Linux | Linux 初始化配置
tags: [Linux]
categories: Linux
abbrlink: linux_init
date: 2023-02-24 22:01:30
---

贼喜欢折腾虚拟机和服务器，Linux 自带的 base 有些不太顺手，然后每次新装系统，就要到处寻找太麻烦，索性自己整理记录一份。

习惯使用了 ubuntu，就用它来整理吧，其实各个 Linux 系统应该也大同小异吧。

<!-- more -->

## 安装 zsh

bash 虽然好用，但 zsh 更好用不是吗 🤔。

简单粗暴的安装命令。

```shell
sudo apt zsh
```

Ubuntu 安装完后，会询问你要不要更改默认 shell 为 zsh，如果不小心错过了，可以手动更改。

```shell
chsh -s /bin/zsh
```

一般 zsh 都在 _/bin/zsh_ 这，不确定的话可以看看。

```shell
ls /bin | grep zsh
```

或者

```shell
which zsh
```

> which 出来的可能不是 /bin/zsh，不过只要它我个人比较喜欢使用 /bin/zsh 的

## on-my-zsh

这个应该不用多说了吧，默认的 zsh 配置起来还是比较繁琐，用这个可以快速美化完成配置。

```shell
wget https://gitee.com/mirrors/oh-my-zsh/raw/master/tools/install.sh
```

下载完后，给 `install.sh` 添加执行权限。

```shell
chmod +x install.sh
```

接下来给脚本换个源，不然国内安装等半天，使用 `vim install.sh` 编辑，找到这里。

```sh
# Default settings
ZSH=${ZSH:-~/.oh-my-zsh}
REPO=${REPO:-ohmyzsh/ohmyzsh}
REMOTE=${REMOTE:-https://github.com/${REPO}.git}
BRANCH=${BRANCH:-master}
```

将中间换成

```sh
REPO=${REPO:-mirrors/oh-my-zsh}
REMOTE=${REMOTE:-https://gitee.com/${REPO}.git}
```

使用 `:wq` 保存退出，然后 `./install.sh` 执行即可。

## 更改主题

比较喜欢 [sosource .zshrc](https://github.com/romkatv/powerlevel10k)，非常的好看和实用。

首先下载字体

- [MesloLGS NF Regular.ttf](https://github.com/romkatv/powerlevel10k-media/raw/master/MesloLGS NF Regular.ttf)
- [MesloLGS NF Bold.ttf](https://github.com/romkatv/powerlevel10k-media/raw/master/MesloLGS NF Bold.ttf)
- [MesloLGS NF Italic.ttf](https://github.com/romkatv/powerlevel10k-media/raw/master/MesloLGS NF Italic.ttf)
- [MesloLGS NF Bold Italic.ttf](https://github.com/romkatv/powerlevel10k-media/raw/master/MesloLGS NF Bold Italic.ttf)

完成后将你的终端软件字体改为 **MesloLGS**

然后安装 p10k

```shell
git clone --depth=1 https://gitee.com/romkatv/powerlevel10k.git ${ZSH_CUSTOM:-$HOME/.oh-my-zsh/custom}/themes/powerlevel10k
```

修改 `~/.zshrc` 里的主题

```sh
ZSH_THEME="powerlevel10k/powerlevel10k"
```

重新执行下

```shell
source .zshrc
```

按照自己喜欢的配置就行啦

macOS 下的效果

![](https://files.zuiyu1818.cn/Linux/powerlevel10k.png)
