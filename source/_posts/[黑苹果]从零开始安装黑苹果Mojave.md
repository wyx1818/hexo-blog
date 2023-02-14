---
title: 黑苹果 | 从零开始安装 macOS 10.14.x Mojave
date: 2019-3-19 21:01:30
tags: 黑苹果
categories: 黑苹果
abbrlink: Hackintosh
---

黑苹果的安装其实很简单，成不成功80%就看EFI驱动是不适合了。

如果你有幸找到了完美适合你电脑的EFI驱动文件，那么恭喜你，不然真正的坑在于后面对驱动的调教

驱动调教参见：

- [黑苹果初级驱动调教](https://zuiyu1818.cn/posts/z7m_Mojave.html)
- [黑苹果进阶驱动定制](https://zuiyu1818.cn/posts/Hac_Advanced.html)

<!-- more -->

## 准备工作

1. 一个8G以上的U盘（有的U盘标的是8G，实际只有7.X，实际容量小于7.5G 的会失败）
2. MacOS 镜像、Transmac
3. 分区工具（DiskGenius）
4. EasyUEFI（企业版的系统要下载 EasyUEFI 企业破解版）
5. EFI文件（网上能找到机子机型的最好，如果没有自己配一个或者试试镜像系统自带的）

<!-- more -->

| 名称                  | 下载地址                                                     |
| --------------------- | ------------------------------------------------------------ |
| macOS镜像(黑果小兵的) | [点击下载](https://pan.baidu.com/s/1wvVtq3aBubu0yKPwjA8g8g)  |
| DiskGenius            | [点击下载](http://www.diskgenius.cn/download.php)            |
| EasyUEFI              | [点击下载](https://www.easyuefi.com/index-cn.html)           |
| 主流配置EFI驱动文件   | [点击下载](https://github.com/sqlsec/clover)                 |
| 以上百度云打包        | [点击下载](https://pan.baidu.com/s/1KkNAkfC9ck7dIv9RszbBoQ)  `密码:6zdm` |

### 安装环境设置

#### BIOS

- SATA模式选择 **AHCI** 模式
- Secure Boot 关掉，不然无法进入Clover
- 操作系统模式选择：**其他操作系统（Other OS）**（看BIOS设置情况）
- 引导模式选择：UEFI（我试了 UEFI + Legcay BIOS 模式也OK）
- 有CSM选项也顺带打开

#### macOS系统盘：

一定要是GPT格式，建议直接找个硬盘单独安装

## 制作安装U盘

### 步骤方法

1. 将您的U盘插入电脑，然后打开`TransMac`，在`TransMac`窗口的左栏找到你的 U 盘。

2. 用鼠标右键单击 U 盘，然后单击`Format Disk for Mac`

   > 这将删除 U 盘上的所有文件，请提前做好数据备份

3. 格式完后选 `Restore with Disk Image`，选择刚刚下载的 `dmg`镜像，确认写入后进行慢慢等待

### 操作演示

我这里的U盘名叫`Newsmy`

![transmac安装](https://cdn.zuiyu1818.cn/mac/transmac.gif)

等待读条完毕即可。(安装时间根据U盘速度而定，请耐心等待)

![TransMac 详解](https://zuiyu-1253240738.cos.ap-beijing.myqcloud.com/Mac/%E8%AF%A6%E8%A7%A3.png)

## 配置驱动

> 这步其实是装黑苹果的核心，装黑苹果能否顺利完成，它起了决定性作用，一些常见问题看文末

将适合你的电脑的EFI驱动文件用`DiskGenius`写入到`U盘`的EFI分区

![拷贝EFI到U盘](https://cdn.zuiyu1818.cn/mac/DS_EFI.gif)

至此可引导macOS的U盘就制作完成了！

## 安装系统

有两种安装系统的方法

- 一个硬盘多个系统（后简称''分区安装''）
- 一个硬盘一个系统（后简称''单盘安装''）←建议

建议单独使用一块硬盘来安装，理由如下

- 这样能提高成功率
- 避免出现意外Windows系统无法进入
- 还有各种乱七八糟的小麻烦

> 无论是哪种方式安装，都需要将磁盘转为`GPT`分区类型！！

### 分区安装

由于macOS只能识别`GUID分区表`，为防止进入安装界面后`磁盘工具`不能识别出目标磁盘，所以先将准备安装macOS的磁盘转为GPT格式。(如果用的是win10系统的话，大部分人都是GPT的)

可以通过下面的方法来查看和更改分区类型

![转换为GPT格式](https://cdn.zuiyu1818.cn/mac/GUID.gif)

#### 扩展ESP分区

> EFI分区大于200MB的可以直接跳过此步

UEFI引导需要ESP分区(EFI system partition)，而macOS需要只是200MB的EFI分区，但装Windows时默认生成的ESP分区只有99MB大小，所以我们需要先扩展Windows的ESP分区

> Windows下的卷标名为`ESP`，macOS显示的卷标名为`EFI`，只是叫法不同罢了

最简单的方法是直接删除MSR分区，将空出来的空间合并到ESP。

> MSR分区是GPT磁盘上备用的分区，例如将磁盘转换为动态磁盘的时会使用到。(一般来说很少用到，所以放心删吧)

**操作演示**

操作有风险，建议先进行备份！！！

这里使用的是`DiskGenius`，理论上讲是可以直接合并的，但是我这不知道为什么不行，所以我演示时直接将ESP和MSR分区都删了，重新建立的。(此方法需要先将ESP分区里的引导Windows的EFI文件夹备份)

![扩展ESP分区](https://cdn.zuiyu1818.cn/mac/ESP_drop_MSR.gif)

#### 准备macOS分区

由于`DiskGenius`操作系统盘时需要重启到PE，所以这里使用Windows自带的磁盘工具(右键左下角win图标打开即可)，系统会占用大约20G空间，建议40G起步最好

![macOS分区](https://cdn.zuiyu1818.cn/mac/mac分区.gif)

#### U盘安装macOS

1. 重启电脑，在开机时选择U盘启动，这时会进入Clover界面，选择`Boot macOS Install from XXXX`（不同Clover版本可能不同），按回车进入，等待读条完毕

> 再次重申！！！
>
> 1. 安装系统的磁盘必须是GPT+UEFI引导
> 2. BIOS一定要开启UEFI模式，有CSM选项也顺带打开
> 3. 一定要关闭Secure Boot，不然无法进入Clover
> 4. 如果启动出现禁止标志，尝试更换USB口，因为mac只能识别USB前15个端口

![](https://zuiyu-1253240738.cos.ap-beijing.myqcloud.com/Mac/Clover.png)

2. 选择你要的语言，点击继续。进入Mojave的实用工具界面，选择`磁盘工具`。选中之前分出来的空间，点击抹掉，格式选择为`APFS`（Mac OS扩展日志式也可以，别选加密就好），输入一个名称(建议英文)

   ![格式为APFS](https://cdn.zuiyu1818.cn/mac/格式化.png)

3. 关闭磁盘工具，选择“安装 macOS”，然后找到自己刚抹掉的那个盘，选中后安装

   ![安装macOS](https://zuiyu-1253240738.cos.ap-beijing.myqcloud.com/Mac/install%20macOS.png)

   ![安装macOS](https://cdn.zuiyu1818.cn/mac/安装macOS.png)

4. 之后跑进度条是转移数据，转移完成后会重启，重启时选择U盘启动进入Clover界面，这时会可能会有不止一个苹果分区（4个）。选择Boot macOS from XXXX（XXXX你刚抹掉那个安装盘的名字），过一会进入正式安装界面。

5. 重启后选择安装好的磁盘分区启动，进入创建用户界面，最后你就进入系统啦

### 单盘安装

其实和分区安装差不多，但是更简单

1. 将硬盘转为GPT(不需要扩展，不需要分区)

   > 主要是怕macOS实用工具识别不出MBR

2. 进入U盘安装，一直格式化这步，选中刚转为GPT格式的硬盘，点击抹掉，方案选择GUID，格式选择APFS，名字随便取

   ![单盘安装](https://cdn.zuiyu1818.cn/mac/单盘安装.png)

3. 后面步骤一样

## 改硬盘启动

1. 把U盘里EFI分区内的EFI文件夹中的clover文件夹，拷贝到装macOS的硬盘的EFI分区

   ![](https://zuiyu-1253240738.cos.ap-beijing.myqcloud.com/Mac/disk.png)

2. 打开`EasyUFEI`，添加硬盘启动项

添加启动项有实操演示和图文步骤两种，自己看下哪个适合

### 操作演示

![实操演示](https://cdn.zuiyu1818.cn/mac/UEFI.gif)

### 图文步骤

1、把刚刚EFI文件夹里的clover文件夹拷贝到装macOS的硬盘的EFI分区，具体不演示了参照前面的相信大家都会

![](https://zuiyu-1253240738.cos.ap-beijing.myqcloud.com/Mac/disk.png)

2、安装easyUEFI，打开，选择管理EFI启动项

![](https://zuiyu-1253240738.cos.ap-beijing.myqcloud.com/Mac/efi1.png)

3、然后添加clover启动项，如下图

![](https://zuiyu-1253240738.cos.ap-beijing.myqcloud.com/Mac/efi2.png)

![](https://zuiyu-1253240738.cos.ap-beijing.myqcloud.com/Mac/efi3.png)

![](https://zuiyu-1253240738.cos.ap-beijing.myqcloud.com/Mac/efi4.png)

4、把刚刚添加的那个引导调到第一

![](https://zuiyu-1253240738.cos.ap-beijing.myqcloud.com/Mac/efi5.png)

至此黑苹果安装教程写完

## 常见问题

> 90%的原因都是EFI驱动问题

### Clover启动后没有出现安装macOS选项

1. 重新制作安装U盘
2. 检查镜像是否完整，或尝试更换镜像

### 开机一直跑代码

1. 镜像文件有问题，检查完整性，或下载其他镜像
2. EFI文件不适合，尝试更换EFI文件(最大可能)

### 出现禁止标志

尝试更换USB口，因为mac只能识别USB前15个端口，实在不行还可以试试2.0的U盘

其他可能同读条卡第二阶段

### 读条卡第二阶段

1. 检查BIOS设置
2. 检查安装磁盘磁盘必须是GPT模式
3. 检查镜像文件是否完整
4. CLOVERX64.efi 文件版本过低，无法引导新系统
5. EFI驱动不适合，尝试更换EFI驱动

### 如果出现无限风火轮/卡在第二阶段进度条且有鼠标

加dart=0参数

### 卷宗抹掉失败

查看EFI分区是否足够200MB

## 注意事项

日后无法通过OS X的“磁盘工具”调整分区；想要调整macOS系统盘的容量的话，就必须先备份系统，回到Windows用“磁盘管理”删除分区，以想要的容量重建，再抹盘恢复系统，这就是为什么我建议单盘安装