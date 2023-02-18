---
title: Linux | 如何挂PT：CentOS 7安装配置美化Transmission及制作种子
tags: [Linux, Transmission]
categories: Linux
abbrlink: transmission
date: 2019-08-13 18:01:30
---

从某位朋友那 py 了台 vps，想着闲着也不能闲着，毕竟怎么说也是个 G 管，刚好最近入了 PT 的坑，那就装个`Transmisson`来挂 PT，成为一个大刷子吧 O(∩_∩)O~~

本文涉及：

- 安装 Transmission 2.94
- web UI 美化
- Transmission 制作种子

<!-- more -->

![transmission](https://files.zuiyu1818.cn/Linux/transmission_WEB.png)

## 安装 Transmission 2.94

### 安装

Transmission 包含在 EPEL 拓展仓库中，如果没有安装 EPEL 源，安装前需要输入以下命令安装 EPEL 源（需要 root 权限）：

```bash
yum -y install epel-release
yum -y update
```

EPEL 源安装成功后，即可安装 Transmission：

```bash
yum install transmission-daemon
```

### 配置

安装成功后，输入`systemctl start transmission-daemon.service`即可启动 Transmission。如果用浏览器打开 web 端（http://域名:9091 或 http:// IP 地址:9091），会提示“403: Forbidden”（页面打不开可能是防火墙没有放行相应端口），这是因为 Transmission 还没配置好。

因而，需要输入`systemctl stop transmission-daemon.service`停止 Transmission 服务，再进行配置。

注意，如果安装后没有启动过 Transmission，是不会生成配置文件。因而，需要先启动服务，再停止服务，生成 json 配置文件后再进行配置。

接下来，就可以打开配置文件：

```bash
vi /var/lib/transmission/.config/transmission-daemon/settings.json
```

配置文件的参数非常多，可按自己需求进行修改。如果对 vi 编辑器不了解，Google 或百度一下就有简单的使用介绍。以下条目的修改是本人自己在用的参数：

```json
"encryption": 2,
“dht-enabled”: false,
"rpc-authentication-required": true,
"rpc-enabled": true,
"rpc-password": "输入你的管理密码",
"rpc-username": "管理你的用户名",
"rpc-whitelist-enabled": false,
```

更多配置参数，可去[Editing-Configuration-Files](https://github.com/transmission/transmission/wiki/Editing-Configuration-Files)探索。

配置好后，保存退出 json 文件，再次输入`systemctl start transmission-daemon.service`启动 Transmission 服务，即可用浏览器打开 web 端（http://域名:9091 或 http:// IP 地址:9091），上传种子进行下载。

如果打不开那要添加防火墙端口

```bash
sudo firewall-cmd --zone=public --add-port=9091/tcp --permanent
sudo firewall-cmd --reload
```

之后检查新的防火墙规则

```
firewall-cmd --list-all
```

## Web UI 美化界面

Transmission 自带的网页 UI 比较简陋，可以安装[transmission-web-control](https://github.com/ronggang/transmission-web-control)进行美化：

```
wget https://github.com/ronggang/transmission-web-control/raw/master/release/install-tr-control.sh --no-check-certificate
bash install-tr-control.sh
```

如果提示

> -bash: wget: 未找到命令

要安装 yum

```bash
yum -y install wget
```

## 设为开机启动

```shell
systemctl enable transmission-daemon
```

再次打开网页，即可使用 transmission-web-control 的 UI。

## 制作种子

从其他站点转资源，需要修改 tracker，可是 linux 下貌似没有修改 tracker 的工具（如有 欢迎小伙伴告知），而无论是 webGUI 还是各个平台的客户端都没有集成这一功能，很蛋疼。

下面写一下如何用 tr 制作种子

### 寻找 tr 安装目录

由于每人安装的可执行目录都不同，所以我们要先找到安装目录，输入：

```bash
find / -name transmission-create
```

![](https://files.zuiyu1818.cn/Linux/tr_find.png)

cd 进这个目录

```bash
cd /usr/bin/
```

### 制作种子

制作很简单，看一个范例

```bash
./transmission-create -p -o /wyx/19.torrent -t https://pt.m-team.cc/announce.php -s 4096 /wyx2/MLB-2019-08-11_ARI\@LAD.mkv
```

参数说明：

- `-p`表示这是私用的种子，这个 PT 必须要加上
- `-o`生成的种子输出位置，不要忘记把名字打上
- `-t`tracker 服务器地址，自行查询各大站点
- `-s`每个文件块的大小，单位是 KB，设置的 4096，也就是 4M
- 最后空一格写源文件的位置，也就是文件的存放位置，可以是一个文件或者一整个目录

填完回车，种子就开始制作了

![](https://files.zuiyu1818.cn/Linux/tr_make.png)

之后将种子下载回来，就可以发布啦

参考链接：

- [如何挂 PT: CentOS 7 安装配置美化 Transmission](https://ylface.com/server/431)
- [用 transmission 直接制作种子的方法](https://tieba.baidu.com/p/5024301968?red_tag=1405790335)
