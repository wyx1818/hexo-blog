---
title: Hexo | 博客文章链接优化
date: 2018-2-22 21:01:30
tags: Hexo
categories: Hexo
abbrlink: NexT_seourl
---

## 前言

URL 地址对于 SEO 中挺重要的，那么如何对链接进行个性化，并在后面加上 `.html` 显得更加正式呢。

Hexo 版本：3.x

答案即将揭晓 🙃

<!-- more -->

## 博客网址链接 URL 唯一且永久化

使用插件[hexo-abbrlink](https://github.com/rozbo/hexo-abbrlink)可以做到自定义文章链接，安装：

```bash
npm install hexo-abbrlink --save
```

然后在 Hexo 的**根目录**的配置文件`_config.yml`中修改：

```yml
permalink: :abbrlink/
#abbrlink配置
abbrlink:
  alg: crc32 # 算法：crc16(default) and crc32
  rep: dec # 进制：dec(default) and hex
```

之后再 md 文件开头中加入以下信息：

```yml
---
abbrlink: seourl
---
```

> 如果文章中未指定 `abbrlink: xxx`，将会根据算法随机生成数字

这样就确保了博文链接的唯一化，只要不修改 md 文件的 `abbrlink` 的值，url 就永久不会改变。如此 md 文件名和文件内容也可以随便改了。这样也有利于 SEO 优化。

## 我的配置

最后附上我的配置，以及相关可以设定的参数

```css
#我的配置，post为固定链接后面带":"的为变量
permalink: posts/:abbrlink.html

#abbrlinks
abbrink:
  alg: crc32
  rep: dec
```

### 官方给出的配置信息

### 变量

除了下列变量外，您还可使用 Front-matter 中的所有属性。

| 变量        | 描述                                                       |
| ----------- | ---------------------------------------------------------- |
| `:year`     | 文章的发表年份（4 位数）                                   |
| `:month`    | 文章的发表月份（2 位数）                                   |
| `:i_month`  | 文章的发表月份（去掉开头的零）                             |
| `:day`      | 文章的发表日期 (2 位数)                                    |
| `:i_day`    | 文章的发表日期（去掉开头的零）                             |
| `:title`    | 文件名称                                                   |
| `:id`       | 文章 ID                                                    |
| `:category` | 分类。如果文章没有分类，则是 `default_category` 配置信息。 |

您可在 `permalink_defaults` 参数下调整永久链接中各变量的默认值：

```css
permalink_defaults:
  lang: en
```

## 参考资料

1. [帆的博客-Hexo 博客 SEO 优化](https://molock.cn/posts/19286/)

2. [永久链接（Permalinks）](https://hexo.io/zh-cn/docs/permalinks.html)
