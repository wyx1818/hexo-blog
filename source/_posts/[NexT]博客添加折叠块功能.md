---
title: NexT | 博客添加折叠块功能
date: 2019-7-13 15:01:30
tags: [NexT,Hexo]
categories: Hexo
abbrlink: NexT_FoldBlock
---

## 前言

有大段的东西想要放上去，但又不想占据大量的位置。折叠是最好的选择。下面在Hexo的主题上定制添加折叠功能。

本文基于Hexo Next的主题修改。其他主题应该也差不多。

<!-- more -->

## 在main.js中添加折叠js

next主题的主要js位于`themes/next/source/js/src/post-details.js`,
在里面找合适的位置，添加如下代码：

{% fold 点击显/隐内容 %}

```
$(document).ready(function(){
    $(document).on('click', '.fold_hider', function(){
        $('>.fold', this.parentNode).slideToggle();
        $('>:first', this).toggleClass('open');
    });
    //默认情况下折叠
    $("div.fold").css("display","none");
});
```
{% endfold %}


## 自定义内建标签

在主题`scripts`下添加一个`tags.js`, 位于`themes/next/scripts/tags.js`

```javascript
/*
  @haohuawu
  修复 Nunjucks 的 tag 里写 ```代码块```，最终都会渲染成 undefined 的问题
  https://github.com/hexojs/hexo/issues/2400
*/
const rEscapeContent = /<escape(?:[^>]*)>([\s\S]*?)<\/escape>/g;
const placeholder = '\uFFFD';
const rPlaceholder = /(?:<|&lt;)\!--\uFFFD(\d+)--(?:>|&gt;)/g;
const cache = [];
function escapeContent(str) {
  return '<!--' + placeholder + (cache.push(str) - 1) + '-->';
}
hexo.extend.filter.register('before_post_render', function(data) {
  data.content = data.content.replace(rEscapeContent, function(match, content) {
    return escapeContent(content);
  });
  return data;
});
hexo.extend.filter.register('after_post_render', function(data) {
  data.content = data.content.replace(rPlaceholder, function() {
    return cache[arguments[1]];
  });
  return data;
});
```

再继续添加一个`fold.js`

```javascript
/* global hexo */
// Usage: {% fold ???? %} Something {% endfold %}
function fold (args, content) {
    var text = args[0];
    if(!text) text = "点击显/隐";
    return '<div><div class="fold_hider"><div class="close hider_title">' + text + '</div></div><div class="fold">\n' + hexo.render.renderSync({text: content, engine: 'markdown'}) + '\n</div></div>';
}
hexo.extend.tag.register('fold', fold, {ends: true});
```

最后，添加几个自定义样式，位置`themes/next/source/css/_custom/custom.styl`

```css
.hider_title{
    font-family: "Microsoft Yahei";
    cursor: pointer;
}
.close:after{
    content: "▼";
}
.open:after{
    content: "▲";
}
```

最后，在我们需要折叠的地方前后添加便签，示例用法：

```
{% fold 点击显/隐内容 %}
something you want to fold, include code block.
{% endfold %}
```

## 参考

https://www.oyohyee.com/post/Note/fold.html