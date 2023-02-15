---
title: NAS | 群晖安装qBittorrent套件并优化设置、替换UI（非docker安装）
date: 2019-9-6 23:01:30
tags: [NAS,PT]
categories: NAS
abbrlink: NAS_qBittorrent
---

## 前言

自从入手矿渣蜗牛，安装了黑裙辉后，博主就走上了PT这条不归路...群晖低功耗加7*24h的特性，让其成为了PT的利器。从最初的群晖自带的 Download Station，到第三方社群安装 Transmission，然后辛苦搜寻 qBittorrent的spk套件，走过的路和坑可谓不少。下面就来说说经验心得。

本文你讲看到：

- Download Station、Transmission、qBittorrent三款软件的对比
- qBittorrent套件的安装和配置
- qBittorrent替换WebUI

<!-- more -->

## Download Station

![DownloadStation](https://files.zuiyu1818.cn//NAS/DownloadStation.jpg)

emmm，这货一言难尽吧，用的比较少一言难尽吧。这货其实就是个套着壳的Tr。

优点：

- 群晖自带，安装简单
- 有着原生GUI界面，适合新手
- 自带管理软件`DS get`

缺点：

- 看不到啥详细数据，数据控难受啊

## Transmission

![Transmission_WebUI](https://files.zuiyu1818.cn//NAS/Transmission_WebUI.jpg)

保种神器，占用资源小，是几乎所有PT站点保种首选

优点：

- 非常适合保种，万种大佬就是用的它

缺点：

- 不能跳过校验
- 校验时不能下载
- 多站点辅种时，文件块不对会爆红，挺烦的

### 权限

有人反映tr无法下载到其他磁盘，表现为没有下载速度，这其中有权限不对的锅。这里简单说下权限问题

![](https://files.zuiyu1818.cn//NAS/Tr_Auth1.jpg)

![](https://files.zuiyu1818.cn//NAS/Tr_Auth2.jpg)

### tracker替换教程

站点有时会更换tracker服务器，经常见小伙伴们询问怎样批量替换tracker，这里就说一下

1. 获取当前tracker，若无属性选项卡，点击右下角即可展开

   ![](https://files.zuiyu1818.cn//NAS/tr_tacker1.jpg)

   此处记得完整复制 ↓ ，不要只复制个开头！！！

   ![](https://files.zuiyu1818.cn//NAS/tr_tacker2.jpg)

2. 替换tracker

   ![](https://files.zuiyu1818.cn//NAS/tr_tacker3.jpg)

   此功能要求字段完全匹配，所以必须完整复制过来

   ![](https://files.zuiyu1818.cn//NAS/tr_tacker4.jpg)

3. 点击确定，等待完成即可

> 若种子较多，替换时间较长，此时WebUI将无响应不要乱动。
>
> 替换后红种为正常现象，请耐心等待。若几h还是红种状态，手动暂停重开试试，若还是有问题注意关注站点公告

## qBittorrent

热种抢上传必备神器

### 下载地址：

| 软件名称          | 下载地址                                                     |
| ----------------- | ------------------------------------------------------------ |
| qBittorrent 4.1.5 | [点击下载](https://pan.baidu.com/s/17MXmo2eyN9Tq9mJUsfOfeQ)  `3wno` |
| qBittorrent 4.1.7 | [点击下载](https://pan.baidu.com/s/12WdmICeGtuzRxhsTrQn8bg)  `p5gs` |
| 最新版spk         | [点击下载](http://ssd.dlinkddns.com/pub/synology/qbittorrent/) |

### 安装方法：

1. 首先确认已启动`admin`用户。此套件默认运行用户为admin，所以不启用可能会导致安装后无法运行。

   ![确认admin用户已经开启](https://files.zuiyu1818.cn//NAS/admin_sure.jpg)

2. 开启家目录。因为套件的配置文件、种子文件、校验文件等都是默认存放在家目录

   ![启用家目录](https://files.zuiyu1818.cn//NAS/qB_home.jpg)

3. 检查家目录下的`admin`文件夹权限是否正确。不放心的小伙伴可以在权限选项卡再确认下admin是否拥有`完全控制`的权限

   ![检查权限](https://files.zuiyu1818.cn//NAS/admin_auth.jpg)

4. 在套件中心手动安装qBittorrent套件

   ![](https://files.zuiyu1818.cn//NAS/qB_install.jpg)

5. 安装完后，在套件列表里点击即可打开网页控制界面（或者手动使用8085端口访问）。默认用户名`admin`密码`adminadmin`

   ![](https://files.zuiyu1818.cn//NAS/qB_start.jpg)

### 权限设置

有小伙伴反应，使用套件安装的qBittorrent下载无速度，这是因为软件读写需要文件夹拥有admin权限，所以给文件下载路径相应的权限即可

![](https://files.zuiyu1818.cn//NAS/qb_auth.jpg)

### 替换WebUI

目前备用UI尚不完善，对我而言仅仅是多了个方便分类查看tracker的功能，不推荐使用，当然随便尝鲜

这里提供两个大佬的项目

1. [CzBiX](https://github.com/CzBiX/qb-web)
2. [miniers](https://github.com/miniers/qb-web)

> 若出现乱码，在地址栏后面加入` /api/v2/app/setPreferences?json=%7B%22alternative_webui_enabled%22:false%7D`进行返回原本UI
>
> 乱码解决方式，查看自己路径是否正确

#### 1. 下载备用WebUI

![](https://files.zuiyu1818.cn//NAS/qB_web1.jpg)

![](https://files.zuiyu1818.cn//NAS/qB_web2.jpg)

#### 2. 解压文件至群晖

找到所在位置

![](https://files.zuiyu1818.cn//NAS/qB_web3.jpg)

在Web用户界面启用备用Web UI

![](https://files.zuiyu1818.cn//NAS/qB_web4.jpg)

保存即可大功告成啦

![](https://files.zuiyu1818.cn//NAS/qB_web5.jpg)

### qBittorrent高级参数设置

{% note success %}
##### qBittorrent4.2.0版本已经支持网页上修改高级参数了
{% endnote %}

配置文件目录：`/volume3/homes/admin/.config/qBittorrent/qBittorrent.conf `

种子文件目录：`/volume3/homes/admin/.local/share/data/qBittorrent/BT_backup`

> 请根据自己情况自己调整文件路径

默认的web界面很多参数都无法修改，尤其是想修改磁盘缓存。打开配置文件，在 [Preferences] 字段新增一行`Downloads\DiskWriteCacheSize=XXXX `，其中XXXX是缓存大小，单位是MB。

![](https://files.zuiyu1818.cn//NAS/qb_ini.jpg)

