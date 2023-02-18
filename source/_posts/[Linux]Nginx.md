---
title: Linux | Nginx相关操作
tags: [Linux, nginx]
categories: Linux
abbrlink: nginx
date: 2019-04-3 18:01:30
---

主要是记录些 nginx 的常用操作，免得自己到处找，大致有

- 启动，停止，重载
- 查看 nginx 路径
- 彻底删除 nginx

<!-- more -->

## Nginx 常用命令

### 查看 Nginx 配置和版本

```bash
nginx -V
```

会输出 Nginx 和 OpenSSL 的版本和其他信息

```bash
# 输出结果
nginx version: nginx/1.15.7
built by gcc 7.3.0 (Ubuntu 7.3.0-27ubuntu1~18.04)
built with OpenSSL 1.1.1a  20 Nov 2018
TLS SNI support enabled
configure arguments: --user=www --group=www --prefix=/usr/local/nginx --with-openssl=../openssl-1.1.1a --with-openssl-opt=enable-tls1_3 --with-http_v2_module --with-http_ssl_module --with-http_gzip_static_module --with-http_stub_status_module --with-http_sub_module --with-stream --with-stream_ssl_module
```

### 验证 Nginx.conf 配置

```bash
sudo nginx -t
```

显示如下输出就成功了

```bash
# nginx -t 输出
nginx: the configuration file /usr/local/nginx/conf/nginx.conf syntax is ok
nginx: configuration file /usr/local/nginx/conf/nginx.conf test is successful
```

还可以用这个来查看安装目录(包安装的时候)

### 启动 Nginx

检验成功后就可以启动了

直接运行 nginx 可执行文件即可，nginx 会自动读取配置文件目录下的“nginx.conf”配置文件，

```bash
sudo nginx
sudo /usr/local/nginx/sbin/nginx  # 启动自己编译的Nginx，一般是这个目录
```

也可以在后面接“-c”参数来指定配置文件：

```bash
sudo nginx -c /usr/local/nginx/sbin/nginx
```

### 停止和重载

当 nginx 启动后，可以使用“-s”参数向 nginx 管理进程发送信号来控制 nginx：

```bash
sudo nginx -s stop  # 停止
sudo nginx -s reload  # 重载
sudo /usr/local/nginx/sbin/nginx -s stop  # 手动编译时
sudo /usr/local/nginx/sbin/nginx -s reload
```

## 彻底删除 nginx

有天想自己手动编译指定版本的 Nginx 以开启 lts1.3，j 结果老是失败，发现是没卸载掉原先包管理安装的，后面找到了这个方法

### 删除包管理安装的 nginx，–purge 包括配置文件

```bash
sudo apt-get --purge remove nginx
```

### 罗列出与 nginx 相关的软件

```bash
dpkg --get-selections|grep nginx
```

```bash
# 执行结果
libnginx-mod-http-geoip                         install
libnginx-mod-http-image-filter                  install
libnginx-mod-http-xslt-filter                   install
libnginx-mod-mail                               install
libnginx-mod-stream                             install
nginx-common                                    install
nginx-core                                      install
```

### 删除上面查询出的软件

```bash
sudo apt-get --purge remove libnginx-mod-http-geoip
sudo apt-get --purge remove libnginx-mod-http-image-filter
sudo apt-get --purge remove libnginx-mod-http-xslt-filter
sudo apt-get --purge remove libnginx-mod-mail
sudo apt-get --purge remove libnginx-mod-stream
sudo apt-get --purge remove nginx-common
sudo apt-get --purge remove nginx-core
```

这样就可以就基本已经完全卸载掉 nginx 包括配置文件，还可以差看下

### 结束正在运行的 nginx 进程

```bash
ps -ef |grep nginx
```

```bash
# 命令输出
ubuntu    5493  7252  0 17:43 pts/0    00:00:00 grep --color=auto --exclude-dir=.bzr --exclude-dir=CVS --exclude-dir=.git --exclude-dir=.hg --exclude-dir=.svn nginx
```

kill nginx 进程

```bash
kill -7252  # 第二个id
```

## 编译 nginx

### 编译 OpenSSL

1. 安装依赖

   ```bash
   sudo apt update
   sudo apt install build-essential checkinstall zlib1g-dev -y
   ```

2. 获取 OpenSSL 1.1.1a，并解压缩源代码

   ```bash
   wget https://www.openssl.org/source/openssl-1.1.1a.tar.gz
   tar xf openssl-1.1.1a.tar.gz && rm openssl-1.1.1a.tar.gz
   ```

3. 配置 OpenSSL

   ```bash
   cd openssl-1.1.1a
   ./config --prefix=/usr/local/ssl --openssldir=/usr/local/ssl shared zlib
   ```

4. 编译与安装

   ```bash
   make && make test
   make install
   ```

### 编译 nginx

1. 安装依赖

   ```bash
   sudo apt update
   sudo apt-get install build-essential libpcre3 libpcre3-dev zlib1g-dev unzip git
   ```

2. 获取 Nginx 1.15.7

   ```bash
   wget https://nginx.org/download/nginx-1.15.7.tar.gz
   tar xf nginx-1.15.7.tar.gz && rm nginx-1.15.7.tar.gz
   ```

3. 配置 Nginx

   ```bash
   ./configure --user=www --group=www --prefix=/usr/local/nginx --with-openssl=../openssl-1.1.1a --with-openssl-opt='enable-tls1_3' --with-http_v2_module --with-http_ssl_module --with-http_gzip_static_module --with-http_stub_status_module --with-http_sub_module --with-stream --with-stream_ssl_module
   ```

4. 编译与安装

   ```bash
   make
   make install
   ```

参考链接：

1. [Nginx 实用教程（一）：启动、停止、重载配置](https://www.cnblogs.com/foxgab/p/6958510.html)
2. [ubuntu14.04 彻底删除 nginx](https://blog.csdn.net/u010571844/article/details/50819704)
3. [为 Nginx 开启 tls 1.3 支持，顺便编译 openssl1.1.1a](https://www.bennythink.com/tls1_3.html)
