---
title: 前端 | VS Code配置Vue开发环境-Vetur+ESLint+Prettier（2020）
tags: [前端, Vue, VSCode]
categories: 前端
abbrlink: VScode_Vue
date: 2020-03-07 18:01:30
---

学着 python，却不知不觉入了前端的坑，相比起 Django 的模板语法，Vue 实在太好用了，一接触就无法自拔。那么多好看的前端 UI 框架，简直是颜控必备啊。

之前一直用着 PyCharm 通过装 Vue.js 插件来写，但是不知道怎么使用 ESLint 校验，一格式化代码就乱，简直逼死强迫症

想起了万能的 VScode，一搜索相关内容，发现就连尤雨溪大大都在用。得知有个神器插件叫`Vetur`，之后装了一通乱七八糟的插件，发现 vscode 被我搞得非常难用！！！

找网上相关配置，各种推荐看的头晕眼花一贴到 vscode 里，就给种报错，或者压根就没效果。没办法，只能去读各个插件的官方文档，自己来配置吧。

<!-- more -->

本文使用到的插件：

- **Vetur**
- **ESLint**
- Prettier
- EditorConfig for VS Code
- Bracket Pair Colorizer 2
- Live Server
- Better Comments
- Auto Close Tag
- Code Spell Checker

核心就是 Vetur+ESLint，其他的我觉得就是锦上添花的作用

## 快速配置

> 本文的配置是记录`Vue CLI`生成的项目，若不是，请自行处理依赖关系

1. 打开 vscode 的插件安装，搜索上面的插件，一一安装

2. 打开 vscode 的设置，键入以下代码

   ```json
   {
     // 保存时自动格式化代码

     "editor.formatOnSave": true,
     // eslint配置项，保存时自动修复错误
     "editor.codeActionsOnSave": {
       "source.fixAll": true
     },

     // 让vetur使用vs自带的js格式化工具，以便在函数前面加个空格
     "vetur.format.defaultFormatter.js": "vscode-typescript",
     "javascript.format.semicolons": "remove",
     "javascript.format.insertSpaceBeforeFunctionParenthesis": true,
     // 指定 *.vue 文件的格式化工具为vetur
     "[vue]": {
       "editor.defaultFormatter": "octref.vetur"
     },
     // 指定 *.js 文件的格式化工具为vscode自带
     "[javascript]": {
       "editor.defaultFormatter": "vscode.typescript-language-features"
     },
     // 默认使用prettier格式化支持的文件
     "editor.defaultFormatter": "esbenp.prettier-vscode"
   }
   ```

3. 在项目的根目录建立`.eslintrc.js`文件，键入以下代码（Vue CLI 已带）

   ```js
   module.exports = {
     root: true,
     env: {
       node: true,
     },
     extends: ["plugin:vue/essential", "@vue/standard"],
     parserOptions: {
       parser: "babel-eslint",
     },
     rules: {
       "no-console": process.env.NODE_ENV === "production" ? "error" : "off",
       "no-debugger": process.env.NODE_ENV === "production" ? "error" : "off",
     },
   };
   ```

4. 在项目的根目录建立`.prettierrc`文件，键入以下代码

   ```json
   {
     "semi": false,
     "singleQuote": true
   }
   ```

5. 在项目的根目录建立`.editorconfig`文件，键入以下代码（Vue CLI 已带）

   ```
   [*.{js,jsx,ts,tsx,vue}]
   indent_style = space
   indent_size = 2
   trim_trailing_whitespace = true
   insert_final_newline = true
   ```

   至此，即可完成，详细解释请看后面

## Vetur

这个插件是 vscode 能优雅写 Vue 的核心，绝对的神器，它的优点：

- 代码高亮
- 代码片段
- Emmet 语法支持
- 语法错误校验检查
- 格式化代码
- 代码提醒
- 对三方 UI 框架的支持

这里主要说说格式化代码相关，防止和 `ESLint` 打架。

从官方文档可以看出，`Vetur`集成了`prettier`，这就非常棒了。`Vetur`能够让 `*.vue` 文件中不同的块使用不同的格式化方案，`<template>`调用 html 格式化工具，`<script>`低啊用 JavaScript 格式化工具，`<style>`使用样式格式化工具。

默认的格式化工具

```json
{
  "vetur.format.defaultFormatter.html": "prettyhtml",
  "vetur.format.defaultFormatter.css": "prettier",
  "vetur.format.defaultFormatter.postcss": "prettier",
  "vetur.format.defaultFormatter.scss": "prettier",
  "vetur.format.defaultFormatter.less": "prettier",
  "vetur.format.defaultFormatter.stylus": "stylus-supremacy",
  "vetur.format.defaultFormatter.js": "prettier",
  "vetur.format.defaultFormatter.ts": "prettier",
  "vetur.format.defaultFormatter.sass": "sass-formatter"
}
```

重要的是，这些工具已经集成！！！不需要额外下载了。

然后来进行一些设置，解决`Vetur`和`ESlint`的冲突。

打开 vscode 的设置

![VSCode设置](https://files.zuiyu1818.cn/H5/VSCodeSetting.jpg)

找个地方加入以下设置（注意最外层的`{}`）

```json
{
  // 将vetur的js格式化工具指定为vscode自带的
  "vetur.format.defaultFormatter.js": "vscode-typescript",
  // 移除js语句的分号
  "javascript.format.semicolons": "remove",
  // 在函数名后面加上括号，类似这种形式 foo () {}
  "javascript.format.insertSpaceBeforeFunctionParenthesis": true
}
```

这里说说为什么要将 js 的格式化工具改为 vscode 自带的。查阅 _Prettier_ 官方文档，发现不支持在函数名后面加上括号。

这点和 _ESLint_ 冲突了，所以要更改。

## ESLint

vscode 的 _ESLint_ 插件在某个版本已经移除了`"eslint.validate"`这个配置选项，而网上很多教程都是使用的这个，导致一贴进 vscode 的配置文件，就开始保存和不兼容。

在新版的 _ESLint_ 中已经支持了对`*.vue`文件的校验，所以无需再进行这项配置了，只需要添加一个保存时自动修复 ESLint 错误的功能就行了。

在 vscode 的配置文件中加入

```json
{
  // eslint配置项，保存时自动修复错误
  "editor.codeActionsOnSave": {
    "source.fixAll": true
  }
}
```

在项目的根目录（也就是和`package.json`同级的目录）建立`.eslintrc.js`文件，键入以下代码：

> 一般来说使用 _Vue CLI_ 生成的项目，启用 ESLint 后会自动生成这个文件。
>
> 若忘了启用，自己手动 npm 安装 eslint 相关插件即可

```json
module.exports = {
  root: true,
  env: {
    node: true,
  },
  extends: ['plugin:vue/essential', '@vue/standard'],
  parserOptions: {
    parser: 'babel-eslint',
  },
  rules: {
    'no-console': process.env.NODE_ENV === 'production' ? 'error' : 'off',
    'no-debugger': process.env.NODE_ENV === 'production' ? 'error' : 'off',
  },
}
```

## Prettier

_Vueter_ 虽好但只能作用于`*.vue`文件，其他的文件像 `*.js *.html` 等还需要使用 _Prettier_ 来格式化

这里做下简单配置，来让 _Prettier_ 更好的和和 _ESLint_ 配合。在项目的根目录建立`.prettierrc`文件，键入以下代码

```json
{
  "semi": false,
  "singleQuote": true
}
```

这两行代码的作用是去除语句结尾的分号 `;` ，以及使用单引号 `'` 替代双引号 `"`

同时建议在 vscode 的配置文件中加入以下代码：

```json
{
  // 保存时自动格式化代码
  "editor.formatOnSave": true,
  // 默认使用prettier格式化支持的文件
  "editor.defaultFormatter": "esbenp.prettier-vscode",
  // 指定 *.vue 文件的格式化工具为vetur，防止和prettier冲突
  "[vue]": {
    "editor.defaultFormatter": "octref.vetur"
  },
  // 指定 *.js 文件的格式化工具为vscode自带，以符合ESLint规范
  "[javascript]": {
    "editor.defaultFormatter": "vscode.typescript-language-features"
  }
}
```

> 为符合 ESLint 规范，建议将不要使用 Prettier 来对 js 进行格式化，手动指定自带格式化工具

## EditorConfig for VS Code

_EditorConfig_ 主要是用于让 vscode 支持`.editorconfig`文件。

`.editorconfig` 文件中的设置用于在基本代码库中维持一致的编码风格和设置，例如缩进样式、选项卡宽度、行尾字符以及编码等，并且它还是跨编辑器的

#### 你需要和 ESLint 的配置相符

否则会出现你格式化代码以后，却不能通过你的代码校验工具的检验

可以这样说，_EditorConfig_ 是让代码创建前保持规范， _Prettier_ 是让代码保存后保持规范

示例：

```
[*.{js,jsx,ts,tsx,vue}]
indent_style = space
indent_size = 2
trim_trailing_whitespace = true
insert_final_newline = true
```

## Bracket Pair Colorizer 2

> 安装即可，无需额外配置

有了它，你再也不怕找不到匹配的各种括号了。他会给匹配的括号赋予同样的颜色，并且给你圈出范围来

![BracketPairColorizer 插件](https://files.zuiyu1818.cn/H5/BracketPairColorizer.gif)

## Live Server

为静态和动态页面生成一个具有实时重新加载功能的本地开发服务器，可以右击直接在浏览器查看

![Live Server 插件](https://files.zuiyu1818.cn/H5/liveServe.gif)

## Better Comments

更好看的注释，更好的区分各个地方了

![Better Comments 插件](https://files.zuiyu1818.cn/H5/betterComments.png)

## Auto Close Tag

自动闭合标签

## Code Spell Checker

单词校验插件，可以避免很多错误，单词写错造成的 bug，一言难尽，强烈安利

## 后记

为什么不使用下面的插件

- CSS Peek ： 可能某些会造成 CPU 负载巨高
- Auto Rename Tag ：作者说可能存在性能问题

当然，以上都是自己的浅薄之见，欢迎评论交流
