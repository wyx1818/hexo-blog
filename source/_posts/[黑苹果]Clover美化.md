---
title: 黑苹果 | Clover更新和界面美化
date: 2019-4-6 13:01:30
tags: [黑苹果,Clover]
categories: 黑苹果
abbrlink: Hac_Clover
---

## 前言

更新macOS系统，发现`CLOVERX64.efi`版本太低无法引导怎么办？看着Clover里一大堆的启动项太多烦人？开机时Clover巨大无比，看着头疼？使用了别人EFI，不喜欢的Clover主题？每次开机都要手动选择系统？

如果你有这些烦恼，那么可以用本文的方法来试试看。

内容概览：

- CLOVERX64.efi更新
- 关闭多余的Clover启动项
- 增加默认启动系统
- 修改clover分辨率
- 更换clover主题

效果预览：

Clover主题：`BeautyDark256`

![完成效果](https://files.zuiyu1818.cn/img/clover_finish.png)

<!-- more -->

## 所需工具

只用一个就行啦，`Clover Configurator`

| 软件名称            | 下载地址                                                     |
| ------------------- | ------------------------------------------------------------ |
| Clover Configurator | [点击下载](https://mackie100projects.altervista.org/download-clover-configurator/) |
| Clover Bootloader   | [点击下载](https://github.com/Dids/clover-builder/releases)  |
| 百度云              | [点击下载](https://pan.baidu.com/s/1-A_ipXvL-4h1UCzC0XpMGg)`密码:h7s6` |

`clover`配置文件路径：`/EFI/EFI/Clover/config.plist`，挂载方式看下面

## 备份原始文件

折腾前先备份，尤其是更新各种驱动啥的，不然翻车的话就难受了。

> 其实无论干啥，备份是个好习惯

### 步骤思路

挂载`EFI`分区，复制`EFI`文件夹到其他地方

下面是详细步骤，明白的小伙伴可以不用看了。

### 具体步骤

1. 打开`Clover Configurator`，挂载`EFI`分区

   ![挂载EFI](https://zuiyu-1253240738.cos.ap-beijing.myqcloud.com/img/mount_efi.png)

2. 打开`EFI`分区

   ![打开EFI分区](https://zuiyu-1253240738.cos.ap-beijing.myqcloud.com/img/open_efi.png)

3. 将`EFI`文件夹拖到其他位置备份。

   > 这里建议找个u盘，防止没法进系统找不回来

   ![备份EFI文件夹](https://zuiyu-1253240738.cos.ap-beijing.myqcloud.com/img/back_efi.png)

至此，备份完成，放心开始折腾吧！

## 更新CLOVERX64.efi版本

### 为什么要更CLOVERX64.efi？

据我所知，更新macOS后，很多小伙伴无法引导系统了，这其中有个可能的原因呢就是`CLOVERX64.efi`版本不是最新的，毕竟引导黑苹果的核心就是`CLOVERX64.efi`。所以，在升级新的macOS前更新下`CLOVERX64.efi`还是很有必要的。

> ~~其实我就是强迫症，不用最新的难受罢了~~

### 步骤思路

下载最新的`CLOVERX64.efi`，替换旧的

### 具体步骤

这里提供两种方法

#### 法一：

若只想更新`CLOVERX64.efi`文件，直接在[Clover Bootloader](https://github.com/Dids/clover-builder/releases)里下载CLOVERX64.efi，然后替换`/EFI/EFI/Clover/CLOVERX64.efi`即可完成升级。

此方法优点是可以在win和mac下都使用，缺点是随着CLOVERX64.efi升级，`Clover_v2.4k_rXXXX`可能还会升级UEFI Drivers的相关东西，这样的话就丢失了这些升级（用这么久下来，除非大版本更新，不然没啥必要）

#### 法二：

> 若嫌打开软件麻烦或检测较慢，可直接在[Clover Bootloader](https://github.com/Dids/clover-builder/releases)下载`Clover_v2.4k_rXXXX.pkg`文件双击安装

1. 打开`Clover Configurator`，在左边`TOOLS`里找到并点击`Install/Drivers`栏，进入后点击右下角的`Check Now`（建议使用稳定版本，Stable），会获取到你现在使用的Clover版本以及最新版本，如图

   ![Clover版本](https://zuiyu-1253240738.cos.ap-beijing.myqcloud.com/img/clover_origin.png)

2. 点击`Update`(建议勾选Save to destop)，此时会下载最新的CLover安装包.

3. 下载完成后会自动打开，出现如下画面。(如果没有自动打开，就在桌面点击`Clover_2.4k_rxxxx.pkg`打开安装)

   ![安装clover](https://zuiyu-1253240738.cos.ap-beijing.myqcloud.com/img/install_clover.png)

4. **重点来了**，一路点击继续，直到`安装类型`这一项，在这里点击`自定`。

   ![自定安装](https://zuiyu-1253240738.cos.ap-beijing.myqcloud.com/img/install_clover_2.png)

   勾选`仅安装UEFI开机版本`和`安装Clover到EFI系统区`。

   ![更新EFI](https://zuiyu-1253240738.cos.ap-beijing.myqcloud.com/img/install_clover_3.png)

   如还需要更改主题的话，把`开机主题`里的`Clover主题`也选上

   ![安装主题管理](https://zuiyu-1253240738.cos.ap-beijing.myqcloud.com/img/install_clover_4.png)

5. 勾选完后，点击`安装`，等待完成就行

6. 重启，打开`Clover Configurator`，查看版本是否变更。

   ![更新完](https://zuiyu-1253240738.cos.ap-beijing.myqcloud.com/img/clover_update.png)

   如果没有成功，就打开装mac的磁盘(我这里叫macOS)，进入`macOS/EFI/CLOVER/`文件夹将`CLOVERX64.efi`，手动拷贝至`EFI/EFI/CLOVER/`替换原来的

7. 至此，更新Clove版本完成

## 关闭多余的Clover启动项

看着那一堆暂时没有的启动栏是不是非常非常烦呢。

当初我再寻找解决方法时，发现网上那些隐藏启动项的帖子动不动获取这获取那，麻烦的要死，麻烦就算了，我还没成功，于是自己琢磨研究出了下面的方法。保证清楚明了，简单实用，一看就会。

> 隐藏错了不必担心，在Clover选择启动项界面按`F3`即可显示所有启动项

### 步骤思路

获取启动项名称，在配置文件中隐藏

### 具体步骤

1. 打开`Clover configurator`，在左边的`TOOLS`里找到并点击`Boot.log`栏，点击右下角的`Generate log`，出现下图

   ![Boot日志](https://zuiyu-1253240738.cos.ap-beijing.myqcloud.com/img/clover_boot_1.png)

2. 按下`Command+F`调出搜索框，在里面输入`ScanLoader`进行搜索，下图红框圈出来的，位于`[xx]:启动项`就是启动项名

   ![获取启动项名称](https://zuiyu-1253240738.cos.ap-beijing.myqcloud.com/img/clover_boot_2.png)

3. 点击左边`SECTIONS`里的`Gui`栏，在`Hide Volume`里填入你想要隐藏的启动项名称即可,注意不带`'`哦

   ![隐藏多余启动项](https://zuiyu-1253240738.cos.ap-beijing.myqcloud.com/Mac/Gui.png)

4. 重启查看效果吧 ^_^

## 增加默认启动系统

### 步骤思路

找到boot项，增加默认启动项

### 具体步骤

打开`Clover configurator`，在左边`SECTIONS`里找到并点击`Boot`栏，在图中找到`Default Boot Volume`，选择`LastBootedVolume`，这样每次重新开机就好启动上次加载的系统啦

当然，建议设置延时哦，这样方便切换系统

| 选项      | 功能                       |
| --------- | -------------------------- |
| x:Timeout | 延迟x秒启动                |
| -1        | 取消延迟启动               |
| Fast      | 不显示Clover界面，快速启动 |

![增加默认启动系统](https://zuiyu-1253240738.cos.ap-beijing.myqcloud.com/img/boot_default.png)

## 修改Clover分辨率

看到有的小伙伴说不会修改clover界面的分辨率，其实很简单哦，不会的来看看吧

### 步骤思路

在配置文件中找到分辨率栏，进行修改

### 具体步骤

1. 关闭`BIOS`里的`CSM`

   > 这一步很重要，不关闭就无法修改

2. 打开`Clover configurator`，在左边`SECTIONS`里找到并点击`GUI`栏，在图中改成你想要的分辨率就行了

   ![修改分辨率](https://zuiyu-1253240738.cos.ap-beijing.myqcloud.com/img/clover_resolution.png)

## 更换Clover主题

如果别自带的EFI主题太丑，那是非常糟心的事，你还在苦苦忍受么。其实clover有非常非常多的主题，总有一款可以切入你的心扉，快来更换吧。

### 步骤思路

使用`CloverThemeManager`软件，安装自己喜欢的clover主题

### 具体步骤

1. 前面更新Clover时你选择安装clover主题了么，没有就去看看吧。

   ![安装主题管理](https://zuiyu-1253240738.cos.ap-beijing.myqcloud.com/img/install_clover_4.png)

2. 在`启动台里`，点击`CloverThemeManager`

   ![CloverThemeManager](https://zuiyu-1253240738.cos.ap-beijing.myqcloud.com/img/clover_theme_1.png)

3. 等待启动，启动完可能会让你输入管理员密码(因为需要挂载`EFI`分区)

   ![加载CloverThemeManager](https://zuiyu-1253240738.cos.ap-beijing.myqcloud.com/img/clover_theme_2.png)

4. 进入主界面，可以看到非常非常多的主题，选择一个自己喜欢的`install`吧

   ![主界面](https://zuiyu-1253240738.cos.ap-beijing.myqcloud.com/img/clover_theme_3.png)

5. 安装完后，在右下角选择刚才安装的主题就可以啦

   ![选择主题](https://zuiyu-1253240738.cos.ap-beijing.myqcloud.com/img/clover_theme_4.png)

6. 重启查看效果吧

## 结语

短短一篇文章没想到写了近2h，码字排版不易，如果你喜欢迎留言交流和分享哦

有问题最好是直接在博文留言，方便我收集整理问题，我会不定期总结出博文，这样可以更好的帮助其他小伙伴