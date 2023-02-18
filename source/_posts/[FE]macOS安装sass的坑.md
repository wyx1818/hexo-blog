---
title: 前端 | macOS安装sass的坑
tags: [前端, Sass, css]
categories: 前端
abbrlink: Ruby_sass
date: 2020-03-10 18:01:30
---

学习 sass，想在 webstorm 中使用 _File Watchers_ 进行实时编译。按照[Sass 中文网](https://www.sass.hk/install/)的安装过程，发现 macOS 由于权限原因无法顺利安装，经历了不少波折，特此记录下

本文内容：

- 使用 homebrew 安装 ruby
- 通过 gem 安装 sass
- 启用 webstorm 文件监控

<!-- more -->

## 使用 Homebrew 安装 ruby

虽然 macOS 已经自带了 ruby，但是使用系统自带 gem 安装的话很容易遇到一个权限类的报错

```shell
gem install sass
ERROR:  While executing gem ... (Gem::FilePermissionError)
    You don't have write permissions for the /Library/Ruby/Gems/2.6.0 directory.
```

因为系统默认使用的是 Apple 自带的 ruby，使用 gem 尝试往 Apple 自家的库里塞东西，出于系统稳定性考虑，肯定是不让你用的呀

在这我还莽了下，尝试使用 sudo，发现居然还是不给。既然如此，还是自己安装一个 ruby 吧

这里使用的 Homebrew 来进行安装，这个的安装参考[Homebrew](https://brew.sh/)。

```shell
brew install ruby
```

安装完成，会很贴心的告诉你需要添加环境变量

```shell
By default, binaries installed by gem will be placed into:
  /usr/local/lib/ruby/gems/2.7.0/bin

You may want to add this to your PATH.

ruby is keg-only, which means it was not symlinked into /usr/local,
because macOS already provides this software and installing another version in
parallel can cause all kinds of trouble.

If you need to have ruby first in your PATH run:
  echo 'export PATH="/usr/local/opt/ruby/bin:$PATH"' >> ~/.zshrc

For compilers to find ruby you may need to set:
  export LDFLAGS="-L/usr/local/opt/ruby/lib"
  export CPPFLAGS="-I/usr/local/opt/ruby/include"

For pkg-config to find ruby you may need to set:
  export PKG_CONFIG_PATH="/usr/local/opt/ruby/lib/pkgconfig"
```

这里由于我已经将终端切换成了 zsh，所以提示可能有些不同，按着他说的加入环境变量即可

```shell
echo 'export PATH="/usr/local/opt/ruby/bin:$PATH"' >> ~/.zshrc
```

修改后记得重启下终端，检查此时的 ruby 路径

```shell
which ruby
/usr/local/opt/ruby/bin/ruby
```

只要不是`/usr/bin`就好，同时在检查下 ruby 版本

```shell
ruby -v
ruby 2.7.0p0 (2019-12-25 revision 647ee6f091) [x86_64-darwin19]
```

满足 sass 建议的 2.6.x，可以进行下一步了

## 升级 gem 及替换源

跟着官方走，升级一下 gem

```shell
gem update --system //该命令请翻墙一下
gem -v
3.1.2
```

删除替换原 gem 源

```shell
//删除替换原gem源
gem sources --add https://gems.ruby-china.com/ --remove https://rubygems.org/
//打印是否替换成功
gem sources -l
https://gems.ruby-china.com
# 确保只有 gems.ruby-china.com
```

## 安装 sass

输入命令进行安装：

```shell
gem install sass
gem install compass
```

由于不再是往系统敏感路径安装，这次应该很顺利。

但是明明安装显示成功了，可是重启终端后输入 sass 并无反应。输入`gem list`，看到了 sasa 已经存在，那应该还是 path 的原因。

既然已经知道问题所在，那就简单了，找到 ruby 路径。

```shell
which ruby
/usr/local/opt/ruby/bin/ruby
```

使用 Finder 打开一看，果然发现了 sass，复制路径，添加到环境变量

![Sass路径](https://files.zuiyu1818.cn/H5/RubySassPath.jpg)

```shell
echo 'export PATH="/usr/local/lib/ruby/gems/2.7.0/bin:$PATH"' >> ~/.zshrc
```

重启终端，测试

```shell
sass -v
Ruby Sass 3.7.4
```

OK，没问题了

## webstorm 添加实时监控

打开 webstorm 设置，在 File Watchers 添加 Sass 模板即可

![添加Sass模板](https://files.zuiyu1818.cn/H5/WebStormSass.jpg)

![Sass模板详情](https://files.zuiyu1818.cn/H5/WebStormSassDetail.jpg)

完结撒花
