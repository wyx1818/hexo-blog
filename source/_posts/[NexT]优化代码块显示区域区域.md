---
title: NexT | 修改内容区域的宽度
date: 2019-2-23 23:01:30
tags: [NexT, Hexo]
categories: Hexo
abbrlink: NexT_codewidth
---

我们用 Next 主题是发现在电脑上阅读文章时内容两边留的空白较多，这样在浏览代码块时经常要滚动滚动条才能阅读完整，体验不是很好，同时觉得也不太美观，下面提供修改内容区域的宽度的方法。

HexT 主题版本：7.x

<!-- more -->

## 默认

NexT 对于内容的宽度的设定如下：

- 700px，当屏幕宽度 < 1600px
- 900px，当屏幕宽度 >= 1600px
- 移动设备下，宽度自适应

如果你需要修改内容的宽度，同样需要编辑样式文件。

### 样式文件位置

- 主题的布局定义 `Hexo/themes/next/source/css/_schemes/Picses/_layout.styl`
- 样式的用户配置 `Hexo/themes/next/source/css/_custom/custom.styl `

所有的修改原则上尽量不要变动源代码，因此我们编辑用户文件（推荐），新增内容。

> 以下所有修改均为在用户配置文件中，若要修改对应主题布局，则找到相应项自行替换

## Mist 和 Muse

当你使用在 Mist 和 Muse 风格时增加以下内容：

```css
// 修改成你期望的宽度
$content-desktop = 700px

// 当视窗超过 1600px 后的宽度
$content-desktop-large = 900px
```

## Pisces 和 Gemini

当你使用 Pisces 和 Gemini 风格时增加以下内容：

```css
.header {
  width: 80%;
  + tablet() {
    width: 100%;
  }
  + mobile() {
    width: 100%;
  }
}
.container .main-inner {
  width: 80%;
  + tablet() {
    width: 100%;
  }
  + mobile() {
    width: 100%;
  }
}
.content-wrap {
  width: calc(100% - 260px);
  + tablet() {
    width: 100%;
  }
  + mobile() {
    width: 100%;
  }
}
```

## 参考文档：

- Mist 和 Muse：官方配置帮助-[NexT-常见问题](http://theme-next.iissnan.com/faqs.html)
- Pisces 和 Gemini：[代码块看起来太麻烦](https://github.com/iissnan/hexo-theme-next/issues/759#issuecomment-202242848)
