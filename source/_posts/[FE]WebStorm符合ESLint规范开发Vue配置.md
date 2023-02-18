---
title: 前端 | WebStorm 符合ESLint规范开发Vue配置
tags: [前端, Vue, WebStorm]
categories: 前端
abbrlink: vue_webstorm
date: 2020-03-09 18:01:30
---

Jetbrains 全家桶大发好，但是 webstorm 的开发 Vue 时，默认配置可能会和 ESLint 的语法冲突，进行格式化时各种闹心。但是也不用惊慌，简单的配置几下，即可快乐的敲代码啦

本文内容：

- 使用 Vue CLI 手动创建项目
- 启用 ESLint 检查
- 统一缩进风格
- 函数名与空号间加上空格
- 去除 JavaScript 语句结尾分号
- 使用单引号替换双引号

<!-- more -->

## Vue CLI 设置

新建项目时取消 `Use the default project setup (babel, eslint)`的勾选

![新建项目](https://files.zuiyu1818.cn/H5/WebStormInit.jpg)

_Vue CLI_ 使用手动模式进行创建 Vue 项目

![手动创建项目](https://files.zuiyu1818.cn/H5/WebStormManually.jpg)

启用 _Linter / Formatter_ ，其他按需选择

![启用检查器](https://files.zuiyu1818.cn/H5/WebStormLinter.jpg)

在 _Pick a linter / formatter config:_ 选择`Standard`配置

![ESLint](https://files.zuiyu1818.cn/H5/WebStormESLint.jpg)

最终配置如图

![最终配置](https://files.zuiyu1818.cn/H5/WebStormFinally.jpg)

接下来开始配置 webstorm，以便于 IDE 自带的格式化和 ESLint 配置相符

## 启用 ESLint 检查

一般来说使用上面的步骤创建完项目以后，IDE 已经自动为我们打开了这个检查

将 JavaScript 的版本设置为 _ES6_

> `Languages & Frameworks`▶`JavaScript`

![设置ES6](https://files.zuiyu1818.cn/H5/WebStormJSES6.jpg)

启用编辑器的 ESLint 检查

> `Editor`▶`JavaScript`▶`Code Quality Tools`▶`ESLint`

![启用ESLint检查](https://files.zuiyu1818.cn/H5/WebStormJSESLint.jpg)

## 统一缩进风格

将 HTML 的缩进设置为 2，记得和 ESLint 的配置`.eslintrc.js`文件以及`.editorconfig`文件相统一

> `Editor`▶`Code Style`▶`HTML`▶`Tabs and Indents`

![HTML缩进](https://files.zuiyu1818.cn/H5/WebStormHTMLIndent.jpg)

同时解决`*.vue`文件中 `<script>` 块缩进不正确的问题，在 `Other` 选项卡中的 _Do not indent children of:_ 新增 `script` 字段

> `Editor`▶`Code Style`▶`HTML`▶`Other`

![解决script块缩进问题](https://files.zuiyu1818.cn/H5/WebStormHTMLIndentScript.jpg)

将 JavaScript 的缩进也修改为 2

![JavaScript缩进](https://files.zuiyu1818.cn/H5/WebStormJSIndent.jpg)

## 函数名与空号间加上空格

类似这种效果`data () {}`，勾选 _Function declaration parentheses_ 即可实现

> `Editor`▶`Code Style`▶`JavaScript`▶`Spaces`

![函数名与空号间加上空格](https://files.zuiyu1818.cn/H5/WebStormJSSpaceFunction.jpg)

## 去除 JavaScript 语句结尾分号

在 ESLint 中建议语句结尾去除分号`;`，所以需要在格式化 JavaScript 中设置一下

> `Editor`▶`Code Style`▶`JavaScript`▶`Punctuation`

![去除结尾分号](https://files.zuiyu1818.cn/H5/WebStormJSSem.jpg)

## 使用单引号替换双引号

ESLint 建议使用单引号`'`替换双引号`"`，在刚刚的选项卡中设置一下

![使用单引号](https://files.zuiyu1818.cn/H5/WebStormJSQuotes.jpg)

## 对象内部的空格处理

就像这样`{ User }`，勾选 _Object literal braces_ 即可实现

> `Editor`▶`Code Style`▶`JavaScript`▶`Spaces`

## 结尾

暂时想到这么多，以后有空再补充
