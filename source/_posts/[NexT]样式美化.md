---
title: NexT | 主题CSS美化
date: 2019-2-24 15:01:30
tags: [NexT, Hexo]
categories: Hexo
abbrlink: NexT_CSS
---

博客有的地方不合自己口味，那就动手来修改美化吧

<!-- more -->

# 配置文件

所有的修改原则上尽量不要变动源代码，因此我们编辑用户文件，新增内容。

用户配置文件地址`/blog/themes/next/source/css/_custom/custom.styl`

# 修改文章的链接样式

```css
// 文章内链接文本样式
.post-body p a {
  color: #0593d3; //原始链接颜色
  border-bottom: none;
  border-bottom: 1px solid #0593d3; //底部分割线颜色
  &:hover {
    color: #fc6423; //鼠标经过颜色
    border-bottom: none;
    border-bottom: 1px solid #fc6423; //底部分割线颜色
  }
}
```

# 代码块链接样式

就是\`\`框起来的内容啦

```css
// 代码样式
code {
  color: #ff7600;
  background: #fbf7f8;
  margin: 2px;
}
```
