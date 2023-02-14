---
title: Hexo | 科学公式渲染
date: 2019-4-8 16:01:30
tags: Hexo
categories: Hexo
abbrlink: hexo_math
---

当初从WordPress转到Hexo，很大一部分原因就是因为WordPress对科学公式支持太差了，体验简直绝望。

hexo下有2大主流渲染方案，`MathJax`和`Katex`，NexT已经自带了它们，所以只需要做相应的设置启用就好

内容概览

- 启用MathJax
- 启动Katex
- 两者体验对比

<!-- more -->

## 建议须知

本文基于NexT主题体验，建议使用KaTex获得更好的效果

NexT主题配置文件路径：`Hexo/themes/next/_config.yml`

## MathJax

首先打开主题配置文件里`next/_config.yml`对科学公式的支持：

```yaml
math:
  enable: true  # or false
```

再检查下引擎是否为`Mathjax`

```yaml
math:
  engine: mathjax
  #engine: katex
```

然后你需要修改一下默认的`markdown`渲染引擎来实现MathJax的效果。

> 不建议使用pandoc来进行渲染，我自己的测试pandoc对有些md语法兼容不好，而且图注不居中是最难受的

查看: [hexo-renderer-kramed](https://www.npmjs.com/package/hexo-renderer-kramed)

以下操作在你hexo博客的目录下(**不是Hexo/theme/next的目录!**):

```bash
# 替换 `hexo-renderer-kramed` 或者 `hexo-renderer-marked` 等hexo的markdown渲染器
# 你可以在你的package.json里找到hexo的markdwon渲染器，并将其卸载
npm un hexo-renderer-marked --save

# 安装 `hexo-renderer-kramed`

npm i hexo-renderer-kramed --save
```

> 注意只能保留一个渲染器！！！

## Katex

首先禁用`MathJax`（如果你配置过MathJax的话），然后修改你的`next/_config.yml`以便加载`katex.min.css`:

```yaml
math:
  #engine: mathjax
  engine: katex
```

你不需要添加`katex.min.js`来渲染数学方程。相应的你需要卸载你之前的hexo的markdown渲染器，然后安装新的`hexo-renderer-markdown-it-plus`:


```bash
# 替换 `hexo-renderer-kramed` 或者 `hexo-renderer-marked` 等hexo的markdown渲染器
# 你可以在你的package.json里找到hexo的markdwon渲染器，并将其卸载
npm un hexo-renderer-marked --save

# or

npm un hexo-renderer-kramed --save

# 然后安装 `hexo-renderer-markdown-it-plus`
npm i @upupming/hexo-renderer-markdown-it-plus --save
```

注意到 [`hexo-renderer-markdown-it-plus`](https://github.com/CHENXCHEN/hexo-renderer-markdown-it-plus) 已经无人持续维护, 所以我们使用 [`@upupming/hexo-renderer-markdown-it-plus`](https://github.com/upupming/hexo-renderer-markdown-it-plus)。 这份fork的代码使用了 [`@neilsustc/markdown-it-katex`](https://github.com/yzhang-gh/markdown-it-katex) 同时它也是VSCode的插件[Markdown All in One](https://github.com/yzhang-gh/vscode-markdown)所使用的, 所以我们可以获得最新的KaTex功能例如 `\tag{}`。

## 对比

`MathJax`

- 优点

  1. 对`Latex`语法支持全面
  2. NexT对其支持较好有优化(单行过长时会置于容器内，手机体验良好)
  3. 中文不会出现字符警告

- 缺点

  1. 需要页面js加载渲染
  2. kramed渲染器对内联公式的转义字符`\`支持不好

`Katex`

- 优点
  1. 页面加载极快
  2. 渲染器效果好(相对kramed对MathJax的内联公式)
- 缺点
  1. NexT对其优化较差(单行过长，页面会出现位移)
  2. 渲染md时，若出现中文字符会弹出大量警告
  3. 会严重影响Hexo渲染速度

---

参考链接：

- [meloday第三方支持](https://molunerfinn.com/hexo-theme-melody-doc/zh-Hans/third-party-support.html#mathjax)