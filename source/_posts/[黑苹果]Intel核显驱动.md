---
title: 黑苹果 | 使用WhateverGreen驱动Intel核显
date: 2019-7-8 13:01:30
tags: [黑苹果, Intel, 核显]
categories: 黑苹果
abbrlink: Hac_Intel_Graphics
---

很多小伙伴问我核显怎么驱动，为什么我驱动了 HD4600 却只有 7m 缓存，我开机花屏难受等等。。。

说实话，我很幸运用到的 EFI 直接就驱动了 HD4600，所以一直对核显驱动就没怎么上心，直到某天受到群友蓝幽鞭策，才开始查阅相关资料，终于弄懂了前因后果。

文章虽然看起来长，步骤思路很简单：

{% note primary %}

清理以前的驱动 → 获取 IGPU 的设备路径 → 填入 ig-platform-id→ 填入 device-id

{% endnote %}
不要被本文的长度吓到了，举个例子：驱动完 HD4600 也就这点东西：

![](https://files.zuiyu1818.cn/Mac/FB_Final.png)

<!-- more -->

## 务必阅读

### 缩写解释

| 缩写 | 解释                            |
| ---- | ------------------------------- |
| FB   | Framebuffer（缓冲帧）           |
| WEG  | Lilu.kext 和 WhateverGreen.kext |

### 软件界面

出现这种界面，则是使用 Xcode 打开的 Plist 文件，若你觉得 Xcode 太大不想安装，也可以选择[PlistEdit Pro](https://www.fatcatsoftware.com/plisteditpro/)

![Xcode](https://files.zuiyu1818.cn/Mac/Xcode_Graphics_disable.png)

### 下载相关

| 名称                | 地址                                                                               |
| ------------------- | ---------------------------------------------------------------------------------- |
| Clover Configurator | [点击下载](https://mackie100projects.altervista.org/download-clover-configurator/) |
| PlistEdit Pro       | [点击下载](https://www.fatcatsoftware.com/plisteditpro/)                           |
| WhateverGreen       | [点击下载](https://github.com/acidanthera/WhateverGreen)                           |
| Lilu                | [点击下载](https://github.com/acidanthera/Lilu)                                    |
| gfxutil             | [点击下载](https://github.com/acidanthera/gfxutil/releases)                        |

### Clover 目录

所有的 clover 目录都是指`/EFI/EFI/CLOVER`

### DATA 数据填入

由于 Clover 的特性，所有的 DATA 类型数据都必须两两一组倒序填入，例如：`0x0A160000`转换之后就是`0000160A`，如下图：

![](https://files.zuiyu1818.cn/Mac/WEG_framebuffer_patch.png)

## 为什么要使用 Lilu + WhateverGreen

随着`macOS Mojave`的发行，之前的通过`Clover` ▸ `KextsToPatch` 通过修补帧缓冲的方法已经失效了，尤其是 `SkyLake` 及以后架构。您现在必须使用 `Lilu` + `WhateverGreen`+`FB Patcher` 的方式来驱动您的显卡。

WhateverGreen 将取代 Lilu 的所有其他视频补丁插件，它目前已经合并了 WhateverGreen，IntelGraphicsFixup，NvidiaGraphicsFixup，Shiki 和 CoreDisplayFixup

## 启用核显的通常步骤

1. 修正有关设备的 ACPI 名称（核显自身名为 IGPU，英特尔 ® 管理引擎（英文缩写: IMEI）名为 IMEI）。
2. 如若必要，将 核显 / IMEI 的 设备 ID 仿冒为合适的型号。
3. 指定正确的缓冲帧。（**英文: Framebuffer, 下文简称 缓冲帧 为 FB**）（即 AAPL,ig-platform-id（适用于 Ivy Bridge 或更新微架构）或 AAPL,snb-platform-id（仅适用于 Sandy Bridge 微架构）) 一组正确的 FB 应当正确地包含了可用的输出端口以及该核显的其他属性。
4. 某些与核显相关的其他设备中已包含相关属性。

其中，第 1 步和第 4 步由 WhateverGreen 自动完成。其可运行在 macOS 10.8 及更高版本，这大大简化了正确启用核显的步骤。

## 准备工作

1. 在 BIOS 中设置核显所需的内存量（即 预分配 DVMT，英文: DVMT Pre-Allocated）为 32 MB, 64 MB, 96 MB 等，与使用的 FB 值相关。如要使用最大值（英文: DVMT Total），请设为 MAX。

   ![BIOS设置](https://files.zuiyu1818.cn/Mac/bios.png)

2. 移除以下驱动（如果曾经使用）

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

   > 这些驱动文件通常位于 Clover 的`kexts/Other`文件夹中

3. 关闭所有 Clover 中的显卡注入

   - config.plist ▸ Graphics ▸ Inject ▸ ATI = NO
   - config.plist ▸ Graphics ▸ Inject ▸ Intel = NO
   - config.plist ▸ Graphics ▸ Inject ▸ NVidia = NO
   - config.plist ▸ Graphics ▸ ig-platform-id = `清空`
   - config.plist ▸ Devices ▸ FakeID ▸ IntelGFX = `清空`

   ![关闭Graphics显卡注入](https://files.zuiyu1818.cn/Mac/Clover_Graphics_disable.png)

   ![关闭Device中的显卡注入](https://files.zuiyu1818.cn/Mac/Clover_Graphics_disable.png)

   如果你还不确定还可以打开 plist 文件查看是否显示为 NO

   ![Xcode](https://files.zuiyu1818.cn/Mac/Xcode_Graphics_disable.png)

4. 禁用 Clover 中 Apci 的以下 DSDT 补丁

   - AddHDMI
   - FixDisplay
   - FixIntelGfx
   - AddIMEI
   - FixHDA

   ![关闭DSDT补丁](https://files.zuiyu1818.cn/Mac/Clover_Acpi_DSDT.png)

   ![关闭DSDT补丁](https://files.zuiyu1818.cn/Mac/Clover_Acpi_DSDT2.png)

5. 关闭 Clover 中 Devices 的`UseIntelHDMI`

   ![关闭UseIntelHDMI](https://files.zuiyu1818.cn/Mac/Clover_Devices_UseInterHDMIpng.png)

6. 删除引导参数：`-disablegfxfirmware`

   ![删除引导参数](https://files.zuiyu1818.cn/Mac/Clover_boot_HD.png)

7. 删除以下位置所有的和 IGPU、HDMI 相关条目（一般来说清空就行了）：

   - config.plist ▸ Devices ▸ Arbitrary
   - config.plist ▸ Devices ▸ Properties
   - config.plist ▸ Devices ▸ AddProperties

   ![删除IGPU](https://files.zuiyu1818.cn/Mac/Clover_Device_IGPU.png)

8. 删除或禁用以下 ACPI 重命名补丁: `GFX0 to IGPU`, `PEGP to GFX0`, `HECI to IMEI`, `MEI to IMEI`, `HDAS to HDEF`, `B0D3 to HDAU`

   ![禁用Acpi补丁](https://files.zuiyu1818.cn/Mac/Clover_Acpi_Patch.png)

若以上都做完了，恭喜你完成了清理工作。

## 添加 Lilu + WhateverGreen 驱动

下载[Lilu](https://github.com/acidanthera/Lilu) 和 [WhateverGreen](https://github.com/acidanthera/WhateverGreen)驱动，选择 release 版本，解压并将.kext 文件置于 Clover 的`kexts/Other`文件夹中

> 若你想要查看调试输出信息，请选择两者的的 debug 版本

## 获取 IGPU 的设备路径

下载并使用[gfxutil](https://github.com/acidanthera/gfxutil/releases)工具，将 gfxutil 文件解压至桌面，打开终端输入如下代码：

```
$ cd Desktop
$ ./gfxutil -f IGPU
DevicePath = PciRoot(0x0)/Pci(0x2,0x0)
```

其中 DevicePath 后面显示的`PciRoot(0x0)/Pci(0x2,0x0)`就是 IGPU 的设备路径

## ig-platform-id（核心步骤）

我们需要制定正确的 Framebuffer，一组正确的正确的 FB 应当正确地包含了可用的输出端口以及该核显的其他属性，所以我们需要注入属性。

打开 config.plist，并在 Device 中的`Properties` 添加以下内容：

- `AAPL,ig-platform-id` 或 `AAPL,snb-platform-id（仅适用于 Sandy Bridge 微架构）`
- 设备 `IGPU` 的 `device-id`（需要仿冒时）
- 设备 `IMEI` 的 `device-id`（需要仿冒时）
- 部分补丁设定（必要时）

> 注意逗号区分中英文！！！

上述属性应使用十六进制代码表示，并且需要 {% label primary@两两一组 %} {% label danger@倒序 %} 输入。如 `0x0A260006` 应该用 `0600260A` 表示

{% note warning %}

### 很多人问倒序怎么倒的，这里画个图解释一下

`0x`指 16 进制，在这咱可以不管，提取出后面的`0A260006`，两两一组`0A 26 00 06`过程如下图所示，最终得到`0600260A`

{% endnote %}

![](https://files.zuiyu1818.cn/Mac/daoxu.jpg)

下面分别提供了适用于不同微架构的常用 `IGPU` 和 `IMEI` 属性模版。

![Xcode](https://files.zuiyu1818.cn/Mac/FB_template_xcode.png)

![clover中的设置](https://files.zuiyu1818.cn/Mac/FB_template_Clover.png)

> 如果某个属性不是必需的，请完全删除掉；如果某个 `PciRoot` 位置不存在，也请彻底删除！

**选择一个适合的 FB。** 首先试试推荐值，如果失败，则逐个尝试其他值.在寻找合适的 FB 时，可以临时通过启动参数设置，此时 `Properties` 部分中的 FB 设置将被忽略。如: `igfxframe=0x0166000B`

![启动参数设置igfxframe](https://files.zuiyu1818.cn/Mac/Clover_Boot_Graphics.png)

{% note info %}

**注意！** 此处格式与 `Properties` 部分的格式不同，这里应{% label danger@正序 %} 输入并保留 `0x` 前缀，如上例所示。

- 如未指定 FB，将会使用默认值；
- 如未设定 FB 并且存在独立显卡，将使用一组空 FB。

{% endnote %}

### HD 2000/3000（[Sandy Bridge](https://zh.wikipedia.org/zh-cn/Sandy_Bridge微架構) 微架构，下文简称 SNB）

支持 macOS 10.7 至 10.13.6，本文适用于 10.8 到 10.13.6。在旧版本系统上请使用传统驱动方式。从 macOS 10.14 起，HD 2000/3000 已经不再支持。

此方法无法开启 Metal。

<details><summary>SNB 微架构可用的 FB 列表：（点击此处以展开）</summary>

- 0x00010000 (移动版，4 端口，无 FBMEM)
- 0x00020000 (移动版，1 端口，无 FBMEM)
- 0x00030010 (桌面版，3 端口，无 FBMEM)
- 0x00030020 (桌面版，3 端口，无 FBMEM)
- 0x00030030 (桌面版，无端口，无 FBMEM)
- 0x00040000 (移动版，3 端口，无 FBMEM)
- 0x00050000 (桌面版，无端口，无 FBMEM)
</details>

_推荐的 FB 配置_：

- 0x00030010（桌面版，缺省值）
- 0x00010000（移动版，缺省值）

通常 SNB 平台无需指定 FB，与 `board-id` 相对应的一组 FB 将会被自动使用。不过，在使用不基于 SNB 平台的 SMBios 时，则需指定 FB。（如使用 `HD 3000` + 基于 `Ivy Bridge` 平台的 `MacBookPro9,1` 时，则需指定 FB）

![](https://files.zuiyu1818.cn/Mac/FB_HD2000.png)

> 注意！为 SNB 平台指定 FB 时，属性名应为 `AAPL,snb-platform-id`，这与其他平台不同。

对于桌面版，需设定（仿冒）`device-id` 为 `26010000`。

![](https://files.zuiyu1818.cn/Mac/FB_HD2000_desk.png)

在基于 [7 系列芯片组](https://ark.intel.com/content/www/cn/zh/ark/products/series/98460/intel-7-series-chipsets.html?_ga=2.100876037.569501178.1553421075-527540512.1553334841) 的主板上使用基于 `SNB` 微架构的处理器时（如在 `Z77` 芯片组上使用基于 `SNB` 微架构的 `i7-2600` 时），需设定（仿冒）`IMEI` 的 `device-ID` 为 `3A1C0000`。

![](https://files.zuiyu1818.cn/Mac/FB_HD2000_Z77.png)

### HD 2500/4000（[Ivy Bridge](https://zh.wikipedia.org/zh-cn/Ivy_Bridge微架構) 微架构，下文简称 Ivy）

支持 macOS 10.8 或更新版本。

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

_推荐的 FB 设置_：

- 0x0166000A（桌面版，缺省值）
- 0x01620005（桌面版）
- 0x01660003（移动版，缺省值）
- 0x01660009（移动版）
- 0x01660004（移动版）

在基于 [6 系列芯片组](https://ark.intel.com/content/www/cn/zh/ark/products/series/98461/intel-6-series-chipsets.html?_ga=2.2193906.333725926.1553422863-527540512.1553334841) 的主板上使用基于 `Ivy` 微架构的处理器时（如在 `Z68` 芯片组上使用基于 `Ivy` 微架构的 `i7-3770` 时），需设定（仿冒）`IMEI` 的 `device-ID` 为 `3A1E0000`。（如下所示）

![](https://files.zuiyu1818.cn/Mac/FB_HD2500.png)

### Intel HD Graphics 4200-5200（[Haswell](https://zh.wikipedia.org/zh-cn/Haswell微架構) 微架构）

支持 macOS 10.9 或更新版本。

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

_推荐的 FB 设置_：

- 0x0D220003（桌面版，缺省值）
- 0x0A160000（移动版，缺省值）
- 0x0A260005（移动版，推荐）
- 0x0A260006（移动版，推荐）

对于 桌面版 HD 4400 以及*所有*移动版核显，需设定（仿冒）`IGPU` 的 `device-id` 为 `12040000`。

![](https://files.zuiyu1818.cn/Mac/FB_HD4600.png)

### HD 5300-6300（[Broadwell](https://zh.wikipedia.org/zh-cn/Broadwell微架構) 微架构，下文简称 BDW）

支持 macOS 10.10.2 或更新版本。

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

_推荐的 FB 设置_：

- 0x16220007（桌面版，缺省值）
- 0x16260006（移动版，缺省值）。

### HD 510-580（[Skylake](https://zh.wikipedia.org/zh-cn/Skylake微架構) 微架构，下文简称 SKL）

支持 macOS 10.11.4 或更新版本。

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

_推荐的 FB 设置_：

- 0x19120000（桌面版，缺省值）
- 0x19160000（移动版，缺省值）

### HD 610-650（[Kaby Lake](https://zh.wikipedia.org/zh-cn/Kaby_Lake微架構) 微架构，下文简称 KBL）

支持 macOS 10.12.6 或更新版本。

<details><summary>KBL 平台可用的 FB 列表：（点击此处以展开）</summary>

- 0x591E0000 (移动版，3 端口，无 FBMEM，35 MB)
- 0x59160000 (移动版，3 端口，无 FBMEM，35 MB)
- 0x59230000 (桌面版，3 端口，无 FBMEM，39 MB)
- 0x59260000 (桌面版，3 端口，无 FBMEM，39 MB)
- 0x59270000 (桌面版，3 端口，无 FBMEM，39 MB)
- 0x59270009 (移动版，3 端口，无 FBMEM，39 MB)
- 0x59120000 (桌面版，3 端口，无 FBMEM，39 MB)
- 0x591B0000 (移动版，3 端口，39 MB)
- 0x591E0001 (移动版，3 端口，无 FBMEM，39 MB)
- 0x59180002 (移动版，无端口，无 FBMEM，1 MB)
- 0x59120003 (移动版，无端口，无 FBMEM，1 MB)
- 0x59260007 (桌面版，3 端口，79 MB)
- 0x59270004 (移动版，3 端口，无 FBMEM，58 MB)
- 0x59260002 (移动版，3 端口，无 FBMEM，58 MB)
- 0x591B0006 (移动版，1 端口，无 FBMEM，39 MB)
</details>

_推荐的 FB 设置_：

- 0x59160000（桌面版，缺省值）
- 0x59120000（桌面版，推荐）
- 0x591B0000（移动版，缺省值）

对于 UHD 620 ([Kaby Lake Refresh](https://en.wikipedia.org/wiki/Kaby_Lake#List_of_8th_generation_Kaby_Lake_R_processors)需设定（仿冒）`IGPU` 的 `device-id` 为 `16590000`

![](https://files.zuiyu1818.cn/Mac/FB_UHD620.png)

### UHD 630（[Coffee Lake](https://zh.wikipedia.org/zh-cn/Coffee_Lake微架构) 微架构，下文简称 CFL）

支持 macOS 10.14 或更新版本。

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

_推荐的 FB 设置_：

- 0x3EA50000（桌面版，缺省值）
- 0x3E9B0007（桌面版，推荐）
- 0x3EA50009（移动版，缺省值）

注意：使用第九代 Coffee Lake R 处理器时，需设定（仿冒）`IGPU` 的 `device-id` 为 `923E0000`。（如下所示）

_从 macOS Mojave 10.14.4 起，无需再设定此参数！_

![](https://files.zuiyu1818.cn/Mac/FB_UHD630.png)

## 使用 WEG 自定义 FB 和 端口 补丁

一般来说 WhateverGreen 会自动完成大部分工作，不需要任何额外的 Framebuffer 补丁。

当出现以下情况可能需要使用额外的 Framebuffer 补丁：

- 在 BIOS 中无法设置超过 32M 的 DVMT（framebuffer-stolenmem / framebuffer-fbmem）
- 为 4K 屏设定更大的 VRAM（-unifiedmem）
- 禁用独显(disable-external-gpu)
- 启用支持 4k 的像素时钟补丁（enable-hdmi20）
- 禁用连接器以启用睡眠（framebuffer-pipecount / framebuffer-portcount / framebuffer-conX-type = -1）
- 更改连接器类型以匹配您的系统端口（framebuffer-conX-type）
- 等等

### WEG 支持的自定义补丁列表

#### 语义补丁：

```
framebuffer-patch-enable (启用语义补丁的总开关)

framebuffer-framebufferid (要修改的 FB，一般保持默认即可)

framebuffer-mobile
framebuffer-pipecount
framebuffer-portcount
framebuffer-memorycount
framebuffer-stolenmem(给BIOS中DVMT增加内存大小）：
framebuffer-fbmem
framebuffer-unifiedmem (核显显存大小，调大一点可能能解决花屏）
framebuffer-cursormem (Haswell 专用补丁)
framebuffer-flags

framebuffer-camellia (集成显示控制器，仅与白苹果相关)

framebuffer-conX-enable (启用端口为 X 的修改)
framebuffer-conX-index
framebuffer-conX-busid
framebuffer-conX-pipe
framebuffer-conX-type
framebuffer-conX-flags
framebuffer-conX-alldata (完全替换端口信息)
framebuffer-conX-YYYYYYYY-alldata (在当前 FB 与 YYYYYY 匹配时完全替换端口信息)

X 是端口索引。
```

#### 二进制补丁：

```
framebuffer-patchN-enable (启用第 N 项补丁)
framebuffer-patchN-framebufferid (要修改的 FB，一般保持默认即可)
framebuffer-patchN-find
framebuffer-patchN-replace
framebuffer-patchN-count (要搜索的补丁号迭代数，默认为 1)
N 为补丁索引号: 0, 1, 2, ... 9
```

#### 部分补丁解释：

再次重申，所有 DATA 数据类型需要将数据两两一组倒过来填入，例如：`0x16260006`转换之后就是这样`06002616`填入，如下图：

![](https://files.zuiyu1818.cn/Mac/WEG_framebuffer_patch.png)

当设置内存大小时，你可能想知道 DATA 是怎么计算出来的。用 framebuffer-fbmem 参数举例，当需要设置为 48M 之后它应填入的值是：`00000003`，这个也是转换后的值，所以原来的值应当是`03000000`，这是一个 16 进制的数字，转换成 10 进制是`50331648`。我们知道 1M=1024KB，1KB = 1024B，所以，我们把转换成十进制之后的数字`50331648`除以 1024 然后再除以 1024，得出的结果就是 48 了，所以这串数字代表的就是 48M。

[点击这里前往进制转换网页](https://link.juejin.im/?target=https%3A%2F%2Ftool.lu%2Fhexconvert%2F)

1. `AAPL,ig-platform-id`（设备平台 id，直接影响显卡是否能成功驱动）： 举例一些常用`笔记本`的核显 id（均为 DATA 数据类型）

   - HD4600~HD5200：`0x0A260000`或`0x0A2E0008`
   - HD5300~HD6000：`0x16260006`
   - HD630：`0x3E9B0000`

2. `device-id`（设备 id，可能是能让黑苹果正确显示设备信息，直接使用**无需倒序**）：

   - HD4600~HD5200：`12040000`

   - HD5300~HD6000：`16160000`

   - HD630：`3e9b0000`

     > 具体的`AAPL,ig-platform-id`和`device-id`的使用设置查看前文

3. `framebuffer-patch-enable`（是否启用 framebuffer 补丁，当然启用啊，不启用的话这篇文章还有什么用处）：

   - DATA 数据：`01000000 `-> 1（启用） ` 00000000` -> 0（不启用）

   - NUMBER 数据：`0`（不启用） `1`（启用）

4. `framebuffer-stolenmem`（给 BIOS 中 DVMT 添加一点内存大小，会影响高分屏，这个值必须大于 32M，也不应该过高）：

   - 一般 1080P 屏幕的话，设置为 48M 就够用了：`00003001`

   - 当你的笔记本电脑屏幕是 2k，你可以设置为 64M：`00000004`

   - 4K 屏的话，要设置为 128M：`00000008`

   > 如果你的 BIOS 中可以设置 DVMT 的话并且你设置成为 128M 之后，可以不需要设置这个属性，或者这个属性设置小一点：`00003001` 保险起见，高分屏直接设置成 128M 比较稳，并且保证在 BIOS 能设置 DVMT 的情况下设置在 64M 或以下 （PS：这一部分可能有误，但是最后一句保险起见，高分屏直接设置成 128M 比较稳是试验过的）

5. `framebuffer-unifiedmem`（核显显存大小，调大一点可能能解决花屏）：

   - 2048M：`00000080`
   - 3072M：`000000C0`

6. framebuffer-cursormem（翻译成中文就是光标内存，会影响高分屏，比如高分屏花屏可能就是这个值不够大）：

   - 一般屏幕设置成 9M 大小就好：`00009000`
   - 高分屏的话最好直接设置成 48M：`00000003`

7. `framebuffer-fbmem`（framebuffer 内存大小，会影响高分屏）：

   - 一般屏幕设置成 9M 大小就好：00009000
   - 高分屏的话最好直接设置成 48M：00000003

8. `framebuffer-conN-enable`（N 为数字，显卡第 N 个输出接口是否启用，1 为启用，0 为不启用）：
   - DATA 数据：`01000000` -> 1（启用） ` 00000000` -> 0（不启用）
   - NUMBER 数据：`0`（不启用） ` 1`（启用）
9. `framebuffer-conN-type`（N 为数字，显卡第 N 个输出接口的类型）：
   - HDMI 输出：`00080000`
   - DP 输出：`0004000`
10. `framebuffer-conN-index`（个人理解，显卡第 N 个输出接口的优先级，或者说是设置第 N 个输出口的位置）： 这个按个人需要设置，如果需要屏蔽这个输出口，可以设置成 FFFFFFFF，也就是最大的数字，让它足够靠后，这样就达到了屏蔽效果！

## HDMI 高分屏 60 fps 方案

为核显添加 `enable-hdmi20` 属性，或使用 `-cdfon` 启动参数代替，**否则将会黑屏**。

![](https://files.zuiyu1818.cn/Mac/WEG_HDMI20.png)

参考链接：

- [ Intel Framebuffer patching using WhateverGreen](https://www.tonymacx86.com/threads/guide-intel-framebuffer-patching-using-whatevergreen.256490/)
- [WhateverGreen Manual](https://github.com/acidanthera/WhateverGreen/tree/master/Manual)
- [【黑苹果显卡驱动】通过 Device/properties 给 Framebuffer 打补丁一点经验](https://juejin.im/post/5ce4ecb26fb9a07ea33bec2f)
