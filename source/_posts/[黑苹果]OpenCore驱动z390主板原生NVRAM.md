---
title: 黑苹果 | OpenCore驱动z390主板原生NVRAM
date: 2020-01-30 13:01:30
tags: [黑苹果, OpenCore]
categories: 黑苹果
abbrlink: z390_NVRAM
---

不久前得知 OpenCore 搞定了 Intel 300 系列主板的 NVRAM，可是一直很懒没有行动。

之前 z390 模拟 NVRAM 各种麻烦就算了，成功了好使了一段时间，某天不知道为啥又不好使了，很烦人。

这下 z390 驱动了原生的 NVRAM 硬件，终于可以安心了。

以下教程理论适用于 300 系列的所有主板，但是我只有 z390，其他请自行测试

<!-- more -->

## 清理模拟 NVRAM

如果你之前模拟过 NVRAM，记得按以下步骤清理

### 清理 LogoutHook

1. 终端输入以下命令来获取`LogoutHook `的路径

   ```
   sudo defaults read com.apple.loginwindow LogoutHook
   ```

   如果终端输出的不是一串路径，而是

   ```
   The domain/default pair of (com.apple.loginwindow, LogoutHook) does not exist
   ```

   说明系统内没有安装过 LogoutHook，可以**跳过 2、3 步骤**

2. 删除 `LogoutHook.command` 文件

   ```
   sudo rm -rf $(sudo defaults read com.apple.loginwindow LogoutHook)
   ```

3. 清空`LogoutHook`的触发设置

   ```
   sudo defaults delete com.apple.loginwindow LogoutHook
   ```

### 修改 config

挂载 EFI 目录，打开`/EFI/OC/config.plist`，修改以下设置

| 键                                     | 值  |
| -------------------------------------- | --- |
| Booter → Quirks → DisableVariableWrite | NO  |
| Misc → Security → AllowNvramReset      | YES |
| Misc → Security → ExposeSensitiveData  | 3   |
| NVRAM → LegacyEnable                   | NO  |

- DisableVariableWrite：后续会使用 DSDT 补丁使 300 系列主板支持原生 NVRAM，所以需要设置为`NO`
- AllowNvramReset：在开机引导项中加入重置 NVRAM 缓存功能的选项，为方便清空以前的 NVRAM 我们选择`YES`
- ExposeSensitiveData：设置值为`3`，这样会将 OpenCore 的启动路径和版本储存进 NVRAM
- LegacyEnable：因为不需要模拟 NVRAME 了，所以这里设置为`NO`

### 删除文件

> 如果有就删除

删除`EFI`分区下的`nvram.plist`

删除/`/EFI/OC/Drivers`目录下的两个文件

- VariableRuntimeDxe.efi
- EmuVariableRuntimeDxe.efi

同时清理`config.plist`中`UEFI→Drivers`对应的这两条目

## 重置 NVRAM

重启电脑，进入 OpenCore 然后选择引导界面的重置 NVRAM 选项「或者」按下快捷键 `Command + Option + P + R`

若引导丢失，使用 Windows 重新添加下即可

## 添加 DSDT 补丁

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

重启，若`*`位于你选择的磁盘前则通过打补丁激活原生 NVRAM 操作成功

**参考链接：**

- [精解 OpenCore](https://blog.daliansky.net/OpenCore-BootLoader.html)
