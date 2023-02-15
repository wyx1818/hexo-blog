---
title: 黑苹果 | 4步驱动Intel核显
date: 2019-7-8 23:01:30
tags: [黑苹果,Intel,核显]
categories: 黑苹果
abbrlink: Hac_Intel_Graphics_simple
---

只需4步，轻松驱动Intel核显（#手动滑稽）

清理以前的驱动→添加lilu+WhateverGreen驱动→获取 IGPU 的设备路径→填入ig-platform-id和device-id

看弄完驱动HD4600也就这点东西：

![](https://files.zuiyu1818.cn/mac/FB_Final.png)

本文比较简单，若你还有其他需求，如修改显存大小、支持4k显示屏等，可以查看[详解版本](https://zuiyu1818.cn/posts/Hac_Intel_Graphics.html)

<!-- more -->

## 清理残留

1. 移除以下驱动

   - AzulPatcher4600
   - AppleBacklightFixup
   - CoreDisplayFixup
   - FakePCIID_Intel_HD_Graphics
   - FakePCIID_Intel_HDMI_Audio
   - FakePCIID.kext（不使用其他基于 FakePCIID 的插件时）
   - IntelGraphicsFixup
   - IntelGraphicsDVMTFixup
   - NvidiaGraphicsFixup
   - Shiki
   > 这些驱动文件通常位于Clover的`kexts/Other`文件夹中
2. 关闭所有Clover中的显卡注入
   - config.plist ▸ Graphics ▸ Inject ▸ ATI = NO 
   - config.plist ▸ Graphics ▸ Inject ▸ Intel = NO 
   - config.plist ▸ Graphics ▸ Inject ▸ NVidia = NO 
   - config.plist ▸ Graphics ▸ ig-platform-id = `清空`
   - config.plist ▸ Devices ▸ FakeID ▸ IntelGFX = `清空`
3. 禁用Clover中Apci的以下DSDT补丁
   - AddHDMI 
   - FixDisplay 
   - FixIntelGfx 
   - AddIMEI 
   - FixHDA
4. 删除引导参数：`-disablegfxfirmware
5. 删除以下位置所有的和IGPU、HDMI相关条目（一般来说清空就行了）：
   - config.plist ▸ Devices ▸ Arbitrary
   - config.plist ▸ Devices ▸ Properties
   - config.plist ▸ Devices ▸ AddProperties
6. 删除或禁用以下 ACPI 重命名补丁: `GFX0 to IGPU`, `PEGP to GFX0`, `HECI to IMEI`, `MEI to IMEI`, `HDAS to HDEF`, `B0D3 to HDAU`
7. 移除CLOVER/ACPI/patched里所有和HDMI及核显驱动相关补丁

## 添加Lilu + WhateverGreen驱动

下载[Lilu](https://github.com/acidanthera/Lilu) 和 [WhateverGreen](https://github.com/acidanthera/WhateverGreen)驱动，选择release版本，解压并将.kext文件置于Clover的`kexts/Other`文件夹中

## 获取 IGPU 的设备路径

下载并使用[gfxutil](https://github.com/acidanthera/gfxutil/releases)工具，将gfxutil文件解压至桌面，打开终端输入如下代码：

```
$ cd Desktop
$ ./gfxutil -f IGPU
DevicePath = PciRoot(0x0)/Pci(0x2,0x0)
```

其中DevicePath后面显示的`PciRoot(0x0)/Pci(0x2,0x0)`就是IGPU的设备路径

在config.plist ▸ Devices ▸ Properties中填入设备路径

![](https://files.zuiyu1818.cn/mac/Device_IGPU_path.png)

## ig-platform-id和device-id（核心步骤）

> 7m通常都是ig-platform-id不合适

在config.plist ▸ Devices ▸ Properties中添加以下内容：

- `AAPL,ig-platform-id` 或 `AAPL,snb-platform-id（仅适用于 Sandy Bridge 微架构）`
- 设备 `IGPU` 的 `device-id`（需要仿冒时）
- 设备 `IMEI` 的 `device-id`（需要仿冒时）
- 部分补丁设定（必要时）

DATA属性应使用十六进制代码表示，并且需要两两一组倒序输入。如 `0x0A260006` 应该用 `0600260A` 表示，以HD4600为例，模板如下

![](https://files.zuiyu1818.cn/mac/FB_Final.png)

### 下面给出笔记本常见的核显

1. `AAPL,ig-platform-id`（设备平台id，直接影响显卡是否能成功驱动）： 举例一些常用`笔记本`的核显id（均为DATA数据类型）
   - HD4600~HD5200：`0x0A260000`或`0x0A2E0008`
   - HD5300~HD6000：`0x16260006`
   - HD630：`0x3E9B0000`
2. `device-id`（设备id，可能是能让黑苹果正确显示设备信息，直接使用**无需倒序**）：
   - HD4600~HD5200：`12040000`
   - HD5300~HD6000：`16160000`
   - HD630：`3e9b0000`

## 各代ig-platform-id查询

> 当推荐不好用时，可以尝试详细列表里的
>
> 注意记得倒序，具体看前文

### HD 2000/3000（[Sandy Bridge](https://zh.wikipedia.org/zh-cn/Sandy_Bridge微架構) 微架构，下文简称 SNB）

支持 macOS 10.7 至 10.13.6，本文适用于 10.8 到 10.13.6。在旧版本系统上请使用传统驱动方式。从 macOS 10.14 起，HD 2000/3000 已经不再支持。

此方法无法开启 Metal。

*推荐的 FB 配置*：

- 0x00030010（桌面版，缺省值）
- 0x00010000（移动版，缺省值）

<details><summary>SNB 微架构可用的 FB 列表：（点击此处以展开）</summary>

- 0x00010000 (移动版，4 端口，无 FBMEM)
- 0x00020000 (移动版，1 端口，无 FBMEM)
- 0x00030010 (桌面版，3 端口，无 FBMEM)
- 0x00030020 (桌面版，3 端口，无 FBMEM)
- 0x00030030 (桌面版，无端口，无 FBMEM)
- 0x00040000 (移动版，3 端口，无 FBMEM)
- 0x00050000 (桌面版，无端口，无 FBMEM)
</details>

通常 SNB 平台无需指定 FB，与 `board-id` 相对应的一组 FB 将会被自动使用。不过，在使用不基于 SNB 平台的 SMBios 时，则需指定 FB。（如使用 `HD 3000` + 基于 `Ivy Bridge` 平台的 `MacBookPro9,1` 时，则需指定 FB）

![](https://files.zuiyu1818.cn/mac/FB_HD2000.png)

> 注意！为 SNB 平台指定 FB 时，属性名应为 `AAPL,snb-platform-id`，这与其他平台不同。

对于桌面版，需设定（仿冒）`device-id` 为 `26010000`。

![](https://files.zuiyu1818.cn/mac/FB_HD2000_desk.png)

在基于 [7 系列芯片组](https://ark.intel.com/content/www/cn/zh/ark/products/series/98460/intel-7-series-chipsets.html?_ga=2.100876037.569501178.1553421075-527540512.1553334841) 的主板上使用基于 `SNB` 微架构的处理器时（如在 `Z77` 芯片组上使用基于 `SNB` 微架构的 `i7-2600` 时），需设定（仿冒）`IMEI` 的 `device-ID` 为 `3A1C0000`。

![](https://files.zuiyu1818.cn/mac/FB_HD2000_Z77.png)

### HD 2500/4000（[Ivy Bridge](https://zh.wikipedia.org/zh-cn/Ivy_Bridge微架構) 微架构，下文简称 Ivy）

支持 macOS 10.8 或更新版本。

*推荐的 FB 设置*：

- 0x0166000A（桌面版，缺省值）
- 0x01620005（桌面版）
- 0x01660003（移动版，缺省值）
- 0x01660009（移动版）
- 0x01660004（移动版）

<details><summary>Capri (Ivy) 可用的 FB 列表：（点击此处以展开）</summary>

- 0x01660000 (桌面版，4 端口，24 MB)
- 0x01260006 (桌面版，无端口，无 FBMEM，0 字节)
- 0x01260007 (桌面版，无端口，无 FBMEM，0 字节)
- 0x01260005 (桌面版，3 端口，16 MB)
- 0x01660001 (移动版，4 端口，24 MB)
- 0x01660002 (移动版，1 端口，24 MB)
- 0x01660008 (移动版，3 端口，16 MB)
- 0x01660009 (移动版，3 端口，16 MB)
- 0x01660003 (移动版，4 端口，16 MB)
- 0x01660004 (移动版，1 端口，16 MB)
- 0x0166000A (桌面版，3 端口，16 MB)
- 0x0166000B (桌面版，3 端口，16 MB)
</details>

在基于 [6 系列芯片组](https://ark.intel.com/content/www/cn/zh/ark/products/series/98461/intel-6-series-chipsets.html?_ga=2.2193906.333725926.1553422863-527540512.1553334841) 的主板上使用基于 `Ivy` 微架构的处理器时（如在 `Z68` 芯片组上使用基于 `Ivy` 微架构的 `i7-3770` 时），需设定（仿冒）`IMEI` 的 `device-ID` 为 `3A1E0000`。（如下所示）

![](https://files.zuiyu1818.cn/mac/FB_HD2500.png)

### Intel HD Graphics 4200-5200（[Haswell](https://zh.wikipedia.org/zh-cn/Haswell微架構) 微架构）

支持 macOS 10.9 或更新版本。

*推荐的 FB 设置*：

- 0x0D220003（桌面版，缺省值）
- 0x0A160000（移动版，缺省值）
- 0x0A260005（移动版，推荐）
- 0x0A260006（移动版，推荐）

<details><summary>Azul (Haswell) 可用的 FB 列表：（点击此处以展开）</summary>

- 0x0C060000 (桌面版，3 端口，209 MB)
- 0x0C160000 (桌面版，3 端口，209 MB)
- 0x0C260000 (桌面版，3 端口，209 MB)
- 0x04060000 (桌面版，3 端口，209 MB)
- 0x04160000 (桌面版，3 端口，209 MB)
- 0x04260000 (桌面版，3 端口，209 MB)
- 0x0D260000 (桌面版，3 端口，209 MB)
- 0x0A160000 (桌面版，3 端口，209 MB)
- 0x0A260000 (桌面版，3 端口，209 MB)
- 0x0A260005 (移动版，3 端口，52 MB)
- 0x0A260006 (移动版，3 端口，52 MB)
- 0x0A2E0008 (移动版，3 端口，99 MB)
- 0x0A16000C (移动版，3 端口，99 MB)
- 0x0D260007 (移动版，4 端口，99 MB)
- 0x0D220003 (桌面版，3 端口，52 MB)
- 0x0A2E000A (桌面版，3 端口，52 MB)
- 0x0A26000A (桌面版，3 端口，52 MB)
- 0x0A2E000D (桌面版，2 端口，131 MB)
- 0x0A26000D (桌面版，2 端口，131 MB)
- 0x04120004 (桌面版，无端口，无 FBMEM，1 MB)
- 0x0412000B (桌面版，无端口，无 FBMEM，1 MB)
- 0x0D260009 (移动版，1 端口，99 MB)
- 0x0D26000E (移动版，4 端口，131 MB)
- 0x0D26000F (移动版，1 端口，131 MB)
</details>

对于 桌面版 HD 4400 以及*所有*移动版核显，需设定（仿冒）`IGPU` 的 `device-id` 为 `12040000`。

![](https://files.zuiyu1818.cn/mac/FB_HD4600.png)

### HD 5300-6300（[Broadwell](https://zh.wikipedia.org/zh-cn/Broadwell微架構) 微架构，下文简称 BDW）

支持 macOS 10.10.2 或更新版本。

*推荐的 FB 设置*：

- 0x16220007（桌面版，缺省值）
- 0x16260006（移动版，缺省值）。

<details><summary >BDW 平台可用的 FB 列表：（点击此处以展开）</summary>

- 0x16060000 (桌面版，3 端口，32 MB)
- 0x160E0000 (桌面版，3 端口，32 MB)
- 0x16160000 (桌面版，3 端口，32 MB)
- 0x161e0000 (桌面版，3 端口，32 MB)
- 0x16260000 (桌面版，3 端口，32 MB)
- 0x162B0000 (桌面版，3 端口，32 MB)
- 0x16220000 (桌面版，3 端口，32 MB)
- 0x160E0001 (移动版，3 端口，60 MB)
- 0x161E0001 (移动版，3 端口，60 MB)
- 0x16060002 (移动版，3 端口，56 MB)
- 0x16160002 (移动版，3 端口，56 MB)
- 0x16260002 (移动版，3 端口，56 MB)
- 0x16220002 (移动版，3 端口，56 MB)
- 0x162B0002 (移动版，3 端口，56 MB)
- 0x16120003 (移动版，4 端口，56 MB)
- 0x162B0004 (桌面版，3 端口，56 MB)
- 0x16260004 (桌面版，3 端口，56 MB)
- 0x16220007 (桌面版，3 端口，77 MB)
- 0x16260005 (移动版，3 端口，56 MB)
- 0x16260006 (移动版，3 端口，56 MB)
- 0x162B0008 (桌面版，2 端口，69 MB)
- 0x16260008 (桌面版，2 端口，69 MB)
</details>

### HD 510-580（[Skylake](https://zh.wikipedia.org/zh-cn/Skylake微架構) 微架构，下文简称 SKL）

支持 macOS 10.11.4 或更新版本。

*推荐的 FB 设置*：

- 0x19120000（桌面版，缺省值）
- 0x19160000（移动版，缺省值）

<details><summary>SKL 平台可用的 FB 列表：（点击此处以展开）</summary>

- 0x191E0000 (移动版，3 端口，56 MB)
- 0x19160000 (移动版，3 端口，56 MB)
- 0x19260000 (移动版，3 端口，56 MB)
- 0x19270000 (移动版，3 端口，56 MB)
- 0x191B0000 (移动版，3 端口，56 MB)
- 0x193B0000 (移动版，3 端口，56 MB)
- 0x19120000 (移动版，3 端口，56 MB)
- 0x19020001 (桌面版，无端口，无 FBMEM，1 MB)
- 0x19170001 (桌面版，无端口，无 FBMEM，1 MB)
- 0x19120001 (桌面版，无端口，无 FBMEM，1 MB)
- 0x19320001 (桌面版，无端口，无 FBMEM，1 MB)
- 0x19160002 (移动版，无端口，无 FBMEM，58 MB)
- 0x19260002 (移动版，3 端口，无 FBMEM，58 MB)
- 0x191E0003 (移动版，3 端口，无 FBMEM，41 MB)
- 0x19260004 (移动版，3 端口，无 FBMEM，35 MB)
- 0x19270004 (移动版，3 端口，无 FBMEM，58 MB)
- 0x193B0005 (移动版，4 端口，无 FBMEM，35 MB)
- 0x191B0006 (移动版，1 端口，无 FBMEM，39 MB)
- 0x19260007 (移动版，3 端口，无 FBMEM，35 MB)
</details>

### HD 610-650（[Kaby Lake](https://zh.wikipedia.org/zh-cn/Kaby_Lake微架構) 微架构，下文简称 KBL）

支持 macOS 10.12.6 或更新版本。

*推荐的 FB 设置*：

- 0x59160000（桌面版，缺省值）
- 0x59120000（桌面版，推荐）
- 0x591B0000（移动版，缺省值）

<details><summary>KBL 平台可用的 FB 列表：（点击此处以展开）</summary>

- 0x591E0000 (移动版，3 端口，无FBMEM，35 MB)
- 0x59160000 (移动版，3 端口，无FBMEM，35 MB)
- 0x59230000 (桌面版，3 端口，无FBMEM，39 MB)
- 0x59260000 (桌面版，3 端口，无FBMEM，39 MB)
- 0x59270000 (桌面版，3 端口，无FBMEM，39 MB)
- 0x59270009 (移动版，3 端口，无FBMEM，39 MB)
- 0x59120000 (桌面版，3 端口，无FBMEM，39 MB)
- 0x591B0000 (移动版，3 端口，39 MB)
- 0x591E0001 (移动版，3 端口，无FBMEM，39 MB)
- 0x59180002 (移动版，无端口，无FBMEM，1 MB)
- 0x59120003 (移动版，无端口，无FBMEM，1 MB)
- 0x59260007 (桌面版，3 端口，79 MB)
- 0x59270004 (移动版，3 端口，无FBMEM，58 MB)
- 0x59260002 (移动版，3 端口，无FBMEM，58 MB)
- 0x591B0006 (移动版，1 端口，无FBMEM，39 MB)
</details>

对于 UHD 620 ([Kaby Lake Refresh](https://en.wikipedia.org/wiki/Kaby_Lake#List_of_8th_generation_Kaby_Lake_R_processors)需设定（仿冒）`IGPU` 的 `device-id` 为 `16590000`

![](https://files.zuiyu1818.cn/mac/FB_UHD620.png)

### UHD 630（[Coffee Lake](https://zh.wikipedia.org/zh-cn/Coffee_Lake微架构) 微架构，下文简称 CFL）

支持 macOS 10.14 或更新版本。

*推荐的 FB 设置*：

- 0x3EA50000（桌面版，缺省值）
- 0x3E9B0007（桌面版，推荐）
- 0x3EA50009（移动版，缺省值）

<details><summary>CFL 平台可用的 FB 列表：（点击此处以展开）</summary>

- 0x3EA50009 (移动版，3 端口，无 FBMEM，58 MB)
- 0x3E920009 (移动版，3 端口，无 FBMEM，58 MB)
- 0x3E9B0009 (移动版，3 端口，无 FBMEM，58 MB)
- 0x3EA50000 (移动版，3 端口，无 FBMEM，58 MB)
- 0x3E920000 (移动版，3 端口，无 FBMEM，58 MB)
- 0x3E000000 (移动版，3 端口，无 FBMEM，58 MB)
- 0x3E9B0000 (移动版，3 端口，无 FBMEM，58 MB)
- 0x3EA50004 (移动版，3 端口，无 FBMEM，58 MB)
- 0x3EA50005 (移动版，3 端口，无 FBMEM，58 MB)
- 0x3EA60005 (移动版，3 端口，无 FBMEM，58 MB)
- 0x3E9B0006 (移动版，1 端口，无 FBMEM，39 MB)
- 0x3E9B0007 (桌面版，3 端口，无 FBMEM，58 MB)
- 0x3E920003 (桌面版，无端口，无 FBMEM，1 MB)
- 0x3E910003 (桌面版，无端口，无 FBMEM，1 MB)
- 0x3E980003 (桌面版，无端口，无 FBMEM，1 MB)
</details>

注意：使用第九代 Coffee Lake R 处理器时，需设定（仿冒）`IGPU` 的 `device-id` 为 `923E0000`。（如下所示）

*从 macOS Mojave 10.14.4 起，无需再设定此参数！*

![](https://files.zuiyu1818.cn/mac/FB_UHD630.png)