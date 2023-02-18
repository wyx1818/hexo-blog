---
title: NAS | ESXI-6.7.0 镜像制作、换证书、修改主机名称、端口
tags: [NAS, ESXI]
categories: NAS
abbrlink: ESXI_modify
date: 2020-01-21 18:01:30
---

早早就想玩玩 ESXI 啦，一直心心念念那天能来个 All in one 把软路由、群辉、ubuntu、kodi 啥都全部放在一个机器上。

因为没适合的设备~~没钱~~，所以迟迟没有行动，一天在某个小伙伴的安利下，忍耐不住躁动~~折腾~~的心，掏出了尘封已久的老笔记本，开始了干干干

本文内容：

- ESXI 镜像制作安装
- 更换 ssh 证书
- 修改主机名称
- 修改端口

<!-- more -->

## 啥是 ESXI

> 以下纯属个人瞎扯淡，只是个人见解

它就是个虚拟机，你能装各种各样的系统在上面玩，甚至黑你还能装个黑苹果在上面跑

**优点：**

1. 安装管理虚拟机方便，web 界面做的贼好
2. Type-1 虚拟机管理程序，性能损失极低
3. 商业产品，稳定可靠

**缺点：**

1. 由于面向的是企业客户，对家用主板支持不好（无法开启 IPMI，你无法获取传感器数据）
2. 不支持无线网卡
3. 螃蟹卡要自定义镜像

## 准备安装镜像

### 所需文件

| 名称                | 官网地址                                                                                                     |
| ------------------- | ------------------------------------------------------------------------------------------------------------ |
| ESXI Offline Bundle | [点击前往](https://my.vmware.com/en/web/VMware/info/slug/datacenter_cloud_infrastructure/VMware_vsphere/6_7) |
| VMware PowerCLI     | [点击前往](https://code.vmware.com/web/tool/6.5 R1/vmware-powercli)                                          |
| ESXi-Customizer-PS  | [点击前往](https://www.v-front.de/p/esxi-customizer-ps.html)                                                 |
| rufus               | [点击前往](http://rufus.ie/)                                                                                 |
| 各网卡驱动补丁      | [点击前往](https://vibsdepot.v-front.de/wiki/index.php/List_of_currently_available_ESXi_packages)            |
| 打包下载            | [百度网盘](https://pan.baidu.com/s/1GthhhOEy-aCLpdbuelF10Q)`276n`                                            |

### 准备环境

安装`VMware PowerCLI`，没啥难度一直下一步就行。

完成后会生成桌面快捷方式，不要着急运行，不然你会看到

![不设置环境报错](https://files.zuiyu1818.cn/ESXI/PowellShell_error.jpg)

这时我们需要打开 PowerShell，键入以下命名允许本地脚本执行：

```
Set-ExecutionPolicy -ExecutionPolicy RemoteSigned
```

### 封装镜像

将`ESXi Offline Bundle`、`ESXi-Customizer-PS`、`网卡驱动`放置在同一个文件夹`D:\ESXI`

打开 PowerShell 切换到该目录

```shell
cd D:\ESXI
```

生成自定义镜像：

```shell
./ESXi-Customizer-PS-v2.6.0.ps1 -izip ESXi670-201912001.zip -dpt net55-r8168-8.045a-napi-offline_bundle.zip -load net55-r8168
```

### 制作启动 U 盘

运行 rufus，设备处下拉选择要写的 U 盘，使用“选择”按钮选择刚生成的镜像。“开始”写入，有提示的话都选择推荐

## 安装 ESXI

将制作好的 U 盘插在电脑上， 开机选择它就像，然后一步步按着提示继续即可

若出现以下情况，则是网卡驱动不正确，检查下步骤和驱动文件重新制作安装即可

![网卡驱动不正确](https://files.zuiyu1818.cn/ESXI/ESXI_Adapter.jpg)

## 更换证书

安装完 ESXI 后，使用 Chrome 打死都不让登陆，而 ESXI 又是默认 HTTPS 访问。无奈之下只能掏出 Safari 临时访问，可是总不能每次都换个浏览器吧，所以替换原本的自签证书势在必行。外在加上准备外网访问控制，有个小锁看着也开心呀。

先是尝试直接在 web 端更换，结果，直接无法安装 ε=(´ο ｀\*)))。唉，只能动用万能的 root 了，上 SSH！

![无法导入](https://files.zuiyu1818.cn/ESXI/ESXI_ssh_error.jpg)

**步骤**

找到证书文件 → 修改替换 → 重启 ESXI

### 启用 ssh

出于安全考虑，ESXI 默认是关闭了 SSH 功能的，需要我们手动打开。

![默认关闭无法登陆](https://files.zuiyu1818.cn/ESXI/ESXI_ssh_disable.jpg)

登录 web 控制台，启动 SSH 服务

![启动TSM-SSH](https://files.zuiyu1818.cn/ESXI/ESXI_ssh_webstart.jpg)

之后就可以通过 ssh 工具控制啦 ╭(● ｀ ∀´●)╯

#### 无法访问网页时

若是 web 无法访问，可以去实体机上手动开启

1. 按`F2`输入密码，进入 Customize System 页面
2. 选择`Troubleshoot Options`选项
3. 启用`SSH`

### 更换 SSH 证书

登陆后，切换到证书所在目录

```shell
cd /etc/vmware/ssl
ls
```

![更换证书](https://files.zuiyu1818.cn/ESXI/ESXI_ssh_ls.jpg)

为防止万一，建议备份一下

```shell
cp rui.crt rui.crt.bak
cp rui.key rui.key.bak
```

修改你准备替换的证书

```shell
vi rui.crt
```

同理，替换掉私钥

```shell
vi rui.key
```

> 注意，私钥是只读文件，需要使用`:wq!`强制保存

替换完成后，不是即使生效，需要重启才行

![修改完成](https://files.zuiyu1818.cn/ESXI/ESXI_ssh_PEM.jpg)

## 修改 ESXI 主机名称

ESXI 一般会根据电脑型号名命主机，可以有时候一大串字符实在难看 (“▔□▔)。作为强迫症，这怎么能忍，必须用个自己看着顺眼的名称才行(ฅ´ω`ฅ)。

![这才看着顺眼嘛](https://files.zuiyu1818.cn/ESXI/ESXI_name.jpg)

首先，让 ESXI 进入维护模式

![维护模式](https://files.zuiyu1818.cn/ESXI/ESXI_name_stop.jpg)

SSH 到主机上，编辑`hosts`文件

```shell
vi /etc/hosts
```

修改成你想要的主机名和域名

![img](https://files.zuiyu1818.cn/ESXI/ESXI_name_hosts.jpg)

运行以下命令

```shell
esxcfg-advcfg -s ESXI.lan /Misc/hostname
```

其中`ESXI.lan`记得替换成你刚修改的

重启，然后不要忘了退出维护模式哦( ∙̆ .̯ ∙̆ )

## 修改 ESXI 端口

家宽常用端口都被运营商封了（╬ ￣皿￣），不能坐以待毙啊既然不能通过 80、443 端口来访问，那么就换个姿势呗

![img](https://files.zuiyu1818.cn/ESXI/ESXI_remote.jpg)

**步骤**

修改端口 → 防火墙放通 → 重启服务

### 修改端口

SSH 登入主机，修改`/etc/vmware/rhttpproxy/config.xml`文件

```shell
vi /etc/vmware/rhttpproxy/config.xml
```

找到：

```xml
<!-- HTTP port to be used by the reverse proxy -->
<httpPort>80</httpPort>

<!-- HTTPS port to be used by the reverse proxy -->
<httpsPort>443</httpsPort>
```

修改为你想要的端口，我使用的`8443`然后保存

```xml
<!-- HTTP port to be used by the reverse proxy -->
<httpPort>80</httpPort>

<!-- HTTPS port to be used by the reverse proxy -->
<httpsPort>8443</httpsPort>
```

重启服务生效：

```shell
/etc/init.d/rhttpproxy restart
```

### 开放端口

由于 vmkernel 使用的是内存文件系统，配置、日志、补丁都保存在内存中，直接修改防火墙配置文件`/etc/vmware/firewall/service.xml`会在系统重启后丢失配置。所以我们利用*开机加载启动脚本*的方式来修改防火墙配置。

#### 新建防火墙配置

将配置文件放在 esxi 的存储器中

```shell
cd /vmfs/volumes/datastore1
```

你会得到类似下面的目录

```shell
[root@ESXI:/vmfs/volumes/5e25bfe4-a96ae182-22f8-80fa5b14d053]
```

新建端口配置文件

```shell
vi /vmfs/volumes/datastore1/new-HTTPS.xml
```

键入配置

```xml
<ConfigRoot>
  <service>
    <id>new-HTTPS</id>
    <rule id='0000'>
      <direction>outbound</direction>
      <protocol>tcp</protocol>
      <port type='dst'>8443</port>
    </rule>
    <rule id='0001'>
      <direction>inbound</direction>
      <protocol>tcp</protocol>
      <port type='dst'>8443</port>
    </rule>
    <enabled>true</enabled>
    <required>false</required>
  </service>
</ConfigRoot>
```

### 添加到开机启动

```shell
vi /etc/rc.local.d/local.sh
```

**一定要添加到`exit 0`的前一行**

```shell
#Copy the new firewall rule from vmfs place holder to file system
cp /vmfs/volumes/datastore1/new-HTTPS.xml /etc/vmware/firewall/
#refresh firewall rules
esxcli network firewall refresh
```

手动运行一下：

```shell
/etc/rc.local.d/local.sh
```

之后重启就会自动加载啦

## 参考链接：

- [ESXi 入门指南](https://www.bennythink.com/esxi-tutorial.html)
- [VMware ESXI 添加网卡驱动并生成新的 ISO 镜像](https://www.smbinn.com/vmnetwork.html)
- [更换 ESXi Host SSL 证书](https://www.tutugreen.com/wordpress/replace-esxi-ssl-cert/)
- [ESXi6.7 改 https 连接端口](https://tanst.net/148.html)
- [修改 ESX 或 ESXi 主机名称](https://www.qzkyl.cn/post-265.html)
