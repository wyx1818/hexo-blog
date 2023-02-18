---
title: Unraid | 系统安装
tags: [Unrai]
categories: unraid
abbrlink: unraid_install
date: 2018-02-23 08:01:30
---

## 设置时区

打卡 `SETTINGS->Date and Time`，将时区和 NTP sever 设置成如下

```
ntp1.aliyun.com
s1b.time.edu.cn
ntp3.aliyun.com
hk.pool.ntp.org
```

<!-- more -->

## APP 商店

```
https://gitee.com/BlueBuger/community.applications/raw/master/plugins/community.applications.plg
```

```
lspci | grep Eth
lspci -nnvs 04:00.0
```

```
https://gitee.com/panlibra/unraid-vfio-pci/raw/master/plugins/vfio.pci.plg
```

CA Config Editor

Unassigned Devices

```bash
luci-app-uhttpd
luci-i18n-uhttpd-zh-cn
luci-ssl-openssl
```
