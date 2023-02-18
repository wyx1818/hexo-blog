---
title: 黑苹果 | OpenCore驱动z390主板原生NVRAM
date: 2020-01-30 13:01:30
tags: [黑苹果, OpenCore]
categories: 黑苹果
abbrlink: z390_NVRAM
---

不久前得知OpenCore搞定了Intel 300系列主板的NVRAM，可是一直很懒没有行动。

之前z390模拟NVRAM各种麻烦就算了，成功了好使了一段时间，某天不知道为啥又不好使了，很烦人。

这下z390驱动了原生的NVRAM硬件，终于可以安心了。

以下教程理论适用于300系列的所有主板，但是我只有z390，其他请自行测试

<!-- more -->

## 清理模拟NVRAM

如果你之前模拟过NVRAM，记得按以下步骤清理

### 清理LogoutHook

1. 终端输入以下命令来获取`LogoutHook `的路径

   ```
   sudo defaults read com.apple.loginwindow LogoutHook
   ```

   如果终端输出的不是一串路径，而是

   ```
   The domain/default pair of (com.apple.loginwindow, LogoutHook) does not exist
   ```

   说明系统内没有安装过LogoutHook，可以**跳过2、3步骤**

2. 删除 `LogoutHook.command` 文件

   ```
   sudo rm -rf $(sudo defaults read com.apple.loginwindow LogoutHook)
   ```

3. 清空`LogoutHook`的触发设置

   ```
   sudo defaults delete com.apple.loginwindow LogoutHook
   ```

### 修改config

挂载EFI目录，打开`/EFI/OC/config.plist`，修改以下设置

| 键                                     | 值   |
| -------------------------------------- | ---- |
| Booter → Quirks → DisableVariableWrite | NO   |
| Misc → Security → AllowNvramReset      | YES  |
| Misc → Security → ExposeSensitiveData  | 3    |
| NVRAM → LegacyEnable                   | NO   |

- DisableVariableWrite：后续会使用DSDT补丁使300系列主板支持原生NVRAM，所以需要设置为`NO`
- AllowNvramReset：在开机引导项中加入重置NVRAM缓存功能的选项，为方便清空以前的NVRAM我们选择`YES`
- ExposeSensitiveData：设置值为`3`，这样会将OpenCore 的启动路径和版本储存进 NVRAM
- LegacyEnable：因为不需要模拟NVRAME了，所以这里设置为`NO`

### 删除文件

> 如果有就删除

删除`EFI`分区下的`nvram.plist`

删除/`/EFI/OC/Drivers`目录下的两个文件

- VariableRuntimeDxe.efi
- EmuVariableRuntimeDxe.efi

同时清理`config.plist`中`UEFI→Drivers`对应的这两条目

## 重置NVRAM

重启电脑，进入OpenCore然后选择引导界面的重置 NVRAM 选项「或者」按下快捷键 `Command + Option + P + R`

若引导丢失，使用Windows重新添加下即可

## 添加DSDT补丁

### 下载补丁文件

| 名称         | 地址                                                    |
| ------------ | ------------------------------------------------------- |
| SSDT-PMC.dsl | [点击下载](https://files.zuiyu1818.cn/Mac/SSDT-PMC.dsl) |
| SSDT-PMC.aml | [点击下载](https://files.zuiyu1818.cn/Mac/SSDT-PMC.aml) |

```
SSDT-PMC.dsl`需要使用`MaciASL`编译，懒得话可以直接下载编译好的`SSDT-PMC.aml
```

### 打入补丁

将编译好的`SSDT-PMC.aml`文件放入`/EFI/OC/ACPI`文件夹

打开`/EFI/OC/config.plist`，在`ACPI→Add`里添加补丁文件

![SSDT](https://files.zuiyu1818.cn/Mac/NVRAM_SSDT.jpg)

## 设置启动磁盘

打开`偏好设置/启动磁盘`, 解锁后选择你的 macOS 分区然后 **「锁上」**

![SSDT](https://files.zuiyu1818.cn/Mac/NVRAM_Startup.jpg)

重启，若`*`位于你选择的磁盘前则通过打补丁激活原生NVRAM操作成功

**参考链接：**

- [精解OpenCore](https://blog.daliansky.net/OpenCore-BootLoader.html)