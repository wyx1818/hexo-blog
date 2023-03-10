---
title: 黑苹果 | macOS Catania 10.15 sidecar 开启及使用体验
date: 2020-02-06 13:01:30
tags: [黑苹果]
categories: 黑苹果
abbrlink: Hac_Catania_sidecar
---

随着 10.15 的正式更新，sidecar 也越来越好用了，之前写过一个 beta 版的体验，当时真的非常糟糕。而 10.15 正式版中，实在是太好用了，以至于我忘记了更新一下以前的文章。本文就来说说使用这么久对 sidecar 的相关体验

本文内容：

- 如何在不支持的设备开启
- Sidecar 的连接方式
- 常见问题
- 使用体验

<!-- more -->

## 黑苹果开启 sidecar

若你将机型设置成如下，直接就能支持 sidecar

- Mac（27 英寸，2015 末) 及更新的机型
- MacBook Pro （2016）及更新的机型
- MacBook（2016）及更新的机型
- MacBook Air（2018）
- Mac mini（2018）
- Mac Pro（2019)

不支持的机型，在终端输入如下指令：

```shell
defaults write com.apple.sidecar.display AllowAllDevices -bool true ;

defaults write com.apple.sidecar.display hasShownPref – bool true ;

open /System/Library/PreferencePanes/Sidecar.prefPane
```

## 连接方式

**无线**

若你使用了黑苹果支持的网卡，并完全驱动成功，那么将 iPad 防止旁边即可识别

**有线**

使用数据线，将 iPad 和黑苹果连接在一起

> 我没有测试过如果不装网卡能否识别，请自测

成功后会出现这样的图标，连接即可

![img](https://files.zuiyu1818.cn/Mac/sidecar_conncet.jpg)]

## 常见问题

### 连接超时

若你确定网卡等设置没问题，连接 iPad 出现超时现象，那么就将 macOS 以及 iPad 上的 iCloud 都重新登录试试

### 连接成功黑屏

我之前就遇到了这个现象，查询得知原因是由于我使用的是`5700XT`，BIOS 默认关闭了独显，这时候我们只要去打开核显，并正确注入 Intel 核显的缓冲帧即可

## Sidercar 的使用体验

一句话概括：**「让 iPad 成为 Mac 的第二块屏幕」**

我对 iPhone 没什么感觉，但自己是个 iPad Pro 死忠粉。iPad Pro 伴随了大学三年，借助 iPad Pro 的电子笔记，我从学渣摇身一变成了别人口中的*大佬*，每逢期末就会分享各科笔记帮助同学们复习，所以对 iPad Pro 有着很深的感情。

开始码代码后就一直想着怎么让它成为副屏，之前一直使用着 duet，不过它会占用 CPU 资源，并且还需要一根线链接，很多不便。

macOS 10.15 Catalina 上搭载的「随航」（Sidecar）可以说称得上“黑科技”：

- 不需要线
- 不需要网络（WiFi）
- 只有设备在旁边

### 低延迟

没错，实际体验就是这样，真的黑科技。这些就算了，Sidecar 还做到了低延迟，到了什么地步呢，我直接把 4K 视频拖上去放，虽有轻微卡顿，但是完全能接受，注意，这还只是无线连接的状态！！！而平时只是查阅文档、看代码的时候，完全体验不出来。

### 观感极佳

自从正式版出来后，我基本每天都使用 sidecar 来连接我的黑苹果与 iPad。一个 4K 显示器，加上一块 Retina 屏幕，这感觉真的是体验过就回不去了。在无线连接状态下，iPad Pro 的颜色、分辨率、帧率，真的做的太优秀了，可以说是无可挑剔

### 数位板

如果你和我一样，还有着 Apple Pencil，那么 iPad Pro 还能作为一个数位板来使用。所见即所得，笔尖直接定位屏幕，倾斜，压感都能识别，真的是太强悍了。还有前面所说的低延迟，作为曾经 Surface Pro 5 的用户，自我感觉这个体验比 Surface Pen 好用多了。

![img](https://files.zuiyu1818.cn/Mac/Apple_Pencil.jpg)
