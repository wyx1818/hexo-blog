---
title: Python | PyCharm
tags: [Python,PyCharm]
categories: Python
abbrlink: PY_PyCharm
date: 2018-02-23 08:01:30
---

## 前言

工欲善其事必先利其器，先让我们来安装好“宇宙第一Python”IDE - PyCharm

<!-- more -->

## 下载PyChram

到[PyCharm](https://www.jetbrains.com/pycharm/download/)官网下载安装最新版即可(本文是2017.2.3)，可以找到PyCharm有两个版本，一个专业版，一个自由版本。

![官网安装下载](https://zuiyu-1253240738.cos.ap-beijing.myqcloud.com/Pycharm/%E5%AE%89%E8%A3%85.png)

点击 *DOWNLOAD* 下载即可，这里建议选择安装**专业版本（professiona）**。

## 安装、注册激活

### 安装过程

正常安装即可，到 *PyCharm License activation* 界面时开始激活

![activation 界面](https://zuiyu-1253240738.cos.ap-beijing.myqcloud.com/Pycharm/%E6%BF%80%E6%B4%BB%E7%95%8C%E9%9D%A2.png)

> 不小心跳过也没事，安装打开界面后，在工具栏上找到 *Help* -> *Register*![软件进入激活](https://zuiyu-1253240738.cos.ap-beijing.myqcloud.com/Pycharm/jihuo2.png)

### 正版激活方式

1. 若资金允许，请点击 <https://www.jetbrains.com/idea/buy/> 购买正版
2. 学生凭学生证可[免费申请](https://www.jetbrains.com/student/)正版授权 | 创业公司可[5折购买](https://www.jetbrains.com/shop/eform/startup)正版授权

![优惠购买](https://zuiyu-1253240738.cos.ap-beijing.myqcloud.com/Pycharm/%E6%AD%A3%E7%89%88%E8%B4%AD%E4%B9%B0.png)

### 和谐版激活方式

目前已知三种可以激活的方法:

#### 1、修改系统时间（e.g.比如2050年)

修改系统时间后打开PyChram，选择30天试用，然后不断根据提升操作。在此期间，会提示你什么哈希值不正确，是因为修改时间造成的，接受就好。成功打开pycharm并建立一个工程后就可以关闭了。再将时间改回正确的时间即可。之后你会发现pycharm的试用剩余天数一直是30天。

#### 2、*Activation code* 激活方法

进入<http://idea.lanyus.com/>，按照步骤注册操作，激活示例

![激活示例](https://zuiyu-1253240738.cos.ap-beijing.myqcloud.com/Pycharm/%E6%BF%80%E6%B4%BB.png)

> 这种方式请别忘了在激活界面选择 *Activation code* 哦![Activation code](https://zuiyu-1253240738.cos.ap-beijing.myqcloud.com/Pycharm/Activation-code.png)

#### 3、*License server* 激活方法

在License server选项里边输入 `https://idea.imsxm.com/` 如图所示![License server](https://zuiyu-1253240738.cos.ap-beijing.myqcloud.com/Pycharm/license.png)

## 汉化方法

对于英语不好的菜鸡博主来说，汉化软件是必须的，pycharm的**汉化步骤**：

1. 下载汉化包：<http://pan.baidu.com/s/1gfrKQKN> 密码：w8j3 （如果失效，留言我发最新的
2. 将安装目录下的 lib目录下的`resources_en.jar`文件复制出来，并且更名为`resources_cn.jar`
3. 双击打开`resources_cn.jar`（**注意是打开而不是解压出来**），将下载的汉化包zh_CN目录下的所有文件拖到刚才打开的`resources_cn.jar`文件内的messages目录中，并保存
4. 将resources_cn.jar文件复制回lib目录

> **切记切记**：
>  如果打开后显示乱码，请先删除`resources_cn.jar`，然后打开PyCharm，在菜单上依次选择 **File -> Settings -> Appearance&Behavior -> Appearance -> 选中Override default fonts by(not recommended) Name: Microsoft YaHei (选择任意中文字体)** 然后将`resources_cn.jar`复制到 .\lib 目录，重新打开PyCharm就能正常显示中文了

## 最终效果

![最终效果](https://zuiyu-1253240738.cos.ap-beijing.myqcloud.com/Pycharm/%E5%AE%8C%E7%BB%93la.png)

<center>——End——</center>

感谢阅读，如果对您有帮助，为我点个赞吧^_^