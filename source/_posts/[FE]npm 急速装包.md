---
title: 前端 | npm 急速装包
tags: [前端, npm]
categories: 前端
abbrlink: node_package
date: 2020-03-10 18:01:30
---

公司要接入特定域进行开发，所以被迫从 macOS 回到了 win 阵容，真的各种不习惯啊啊啊。最典型的就是切换中英文输入法，win 是 Shift 切换中英文，而 mac 是 CapsLock ，各种手忙脚乱。

进入正题，win 下如何急速装包：主要思路就是让 `npm` 走代理！

<!-- more -->

## 安装神秘工具

这里安利非常牛叉的 _[Clash for Windows](https://github.com/Fndroid/clash_for_windows_pkg/releases)_ , win 上我用过的最好的神秘作用软件了。

在 Profiles 里导入好你的配置文件，确保自己能够成功出墙，这第一步就完成啦。

## 修改 npm 代理

打开 cmd 或 powershell，通过命令行为 npm 设置代理

```shell
# clash 默认的隧道地址为http://127.0.0.1:7890
npm config set proxy http://127.0.0.1:7890
npm config set https-proxy http://127.0.0.1:7890
```

如果要取消代理，通过

```shell
npm config delete proxy
npm config delete https-proxy
```

## 安装源管理工具 nrm

因为有些包是公司私有的，所有这里使用 nrm 来方便的管理我们的源

```shell
npm install -g nrm
```

安装完后，通过 `nrm add` 添加公司内部源地址

```shell
# test 为别名
nrm add test http://xxx.xxx
```

添加成功后，通过 `nrm ls` 来查看已有的源

```shell
$ nrm ls

* npm -------- https://registry.npmjs.org/
  yarn ------- https://registry.yarnpkg.com/
  cnpm ------- http://r.cnpmjs.org/
  taobao ----- https://registry.npm.taobao.org/
  nj --------- https://registry.nodejitsu.com/
  npmMirror -- https://skimdb.npmjs.com/registry/
  edunpm ----- http://registry.enpmjs.org/
  test-------- http://xxx.xxx
```

通过 `nrm use` 来切换源

```shell
nrm use test
```

### 如果使用 nrm 报错

**情况一：提醒 因为在此系统上禁止运行脚本。有关详细信息，请参阅 https:/go.microsoft.com/fwlink/?LinkID=135170 中的 about_Execution_Policies。**

使用**管理员**运行 powershell，输入

```shell
set-executionpolicy remotesigned
```

之后输入 y

**情况二：internal/validators.js:124 throw new ERR_INVALID_ARG_TYPE(name, ‘string’, value);**

这是因为 _nrm_ 使用了 `process.env.HOME` 来获取我们的用户目录，在 win 下值是 `undefined` ，推荐的方式是使用 _git bash_ 来运行，这样就不会报错啦

## 开始装包

克隆好公司项目，直接使用 `npm i` 来安装，这时应该就会从代理来下载各自的包了。

如果遇到像 node-sass 这些需要从 github 额外下载的数据的包，建议在 `npm i` 之前让命令行走代理

```shell
# 根据你的终端选一个
# cmd
set http_proxy=http://127.0.0.1:7890 & set https_proxy=http://127.0.0.1:7890
# power shell
$Env:http_proxy="http://127.0.0.1:7890";$Env:https_proxy="http://127.0.0.1:7890"
# git bash
git config --global https.proxy http://127.0.0.1:7890
git config --global https.proxy http://127.0.0.1:7890
```

有些包是公司私有源的，遇到 404 报错不要急，使用以下的方式来解决

1. 在报错的信息中找到那个包名，先去 _package.json_ 里删除掉
2. 等公共的包都装玩后，在通过 `git checkout .` 恢复 _package.json_
3. 使用 `nrm use test` 切换到公司源，再 `npm i` 安装

搞定~

## 其他的坑

在此过程中，还遇到了些其他的坑

### 生成环境分支不是默认分支

git clone 克隆下来的分支一般来说是 master，我司的项目生产环境的分支并不是 master，所以克隆的时候要注意下

### 包 hash 校验出错

这个简单，删除掉 package-lock.json 就好

## 结语

或许你会说为啥这要么麻烦啊，还要先去删除掉内部包再一步步来安装。

其实吧这是因为公司内部的镜像源非常的满。。。我之前装了直接切换到内部源，一个项目装了一上午还没装完，简直怀疑人生

踩了比较多的坑，也算是来做个总结吧 ^ \_ ^
