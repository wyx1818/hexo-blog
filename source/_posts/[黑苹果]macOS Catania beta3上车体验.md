---
title: 黑苹果 | macOS Catania 10.15 beta3 上车体验及开启sidecar方法
date: 2019-7-6 13:01:30
tags: 黑苹果
categories: 黑苹果
abbrlink: Hac_Catania_beta3_sidecar
---

先来总结，不建议上车，请等正式版...

说下自己用了一天的体验：

1. 经常出现莫名其妙的鼠标卡顿
2. Chrome 打开速度变慢
3. 系统目录被拆分了，macOS 系统占用一个分区，其他占用另一个分区，这样会导致一些 App 挂掉，比如：百度云
4. imazing 无法使用，猜测可能是因为 iTunes 被移除的原因吧
5. dw1830 蓝牙无法驱动（试了国内外 n 多方法还是不行）
6. sidecar 没有想象的那么好用，duet 完爆体验，所以买了 duet 的小伙伴不用后悔啦
7. 很多软件无法正常工作是应为权限不足的问题，打开 System Preference->Security & Privacy，重新给下权限或者删除重新添加就好了，如 Bartender3 就是这样

下面谈谈上车遇到的问题及我的解决方法。

<!-- more -->

## sidecar 启用方法

然而首个版本可能是因为技术问题，或者硬件问题，苹果并没有为所有设备开放。

在“终端”输入下面的指令，然后重启电脑：

```
defaults write com.apple.sidecar.display allowAllDevices -bool YES
```

重启后屏幕右上角会出现"隔空播放的入口，点击就可以看到 iPad

![AirPlay](https://files.zuiyu1818.cn/Mac/AirPlay_ipad.png)

如果没有出现，点击 Finder 的 Go To Folder 输入`/System/Library/CoreServices/Menu Extras`，找到`Diaplays.menu`双击即可

![Diaplays.menu](https://files.zuiyu1818.cn/Mac/Diaplays.png)

若还不行，将 iPad 插上数据线和电脑连接应该就可以了

### 感受

个人体验下来，大失所望，可能因为还不是正式版的原因吧，sidecar 被 duet 完爆。

在我使用中，最大的感受就是使用 sidecar 在我的 iPad Pro 12.9 3rd 上有花屏的现象，体验巨差！！！

## 更新`Clover Bootloader`

升级系统前请务必确保你的`Clover Bootloader`版本不低于 r4940，更新方法见[Clover 更新和美化](https://zuiyu1818.cn/posts/Hac_Clover.html)

## 声卡无法工作

由于 10.15 删除了大量的布局，导致声卡无法工作，因此需要升级`AppleALC.kext`到 1.3.9 以上才能正常工作

[AppleALC.kext 更新下载地址](https://github.com/acidanthera/AppleALC/releases)

## USB 端口补丁

将端口补丁替换成即可

```
Comment: USB port limit patch #1 10.15.x modify by DalianSky(credit ydeng)
Name: com.apple.iokit.IOUSBHostFamily
Find: 83FB0F0F
Replace: 83FB3F0F

Comment: USB Port limit patch #2 10.15.x modify by DalianSky
Name: com.apple.driver.usb.AppleUSBXHCI
Find: 83F90F0F
Replace: 83F93F0F
```

## HD4600 HDMI 无法工作

在 10.14 都好好的，升级后无法使用，郁闷了我好久。解决办法如下：

打开`Clover Configurator`，设置 smibos 里的 board-id 为下列的：

- Mac-031B6874CF7F642A(\*我用这个就好了)
- Mac-189A3D4F975D5FFC
- Mac-27ADBB7B4CEE8E61
- Mac-35C1E88140C3E6CF
- Mac-4B7AC7E43945597E
- Mac-77EB7D7DAF985301
- Mac-7DF21CB3ED6977E5
- Mac-81E3E92DD6088272
- Mac-C3EC7CD22292981F
- Mac-C9CF552659EA9913
- Mac-FC02E91DDD3FA6A4

![Board-ID](https://files.zuiyu1818.cn/Mac/board-id.png)
