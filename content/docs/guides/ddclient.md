---
title: "Ddclient"
description: ""
summary: ""
date: 2023-12-09T01:23:47+08:00
lastmod: 2023-12-09T01:23:47+08:00
draft: true
menu:
  docs:
    parent: ""
    identifier: "ddclient-ed0f83f8aed5f49bb635ba322afd05f5"
weight: 999
toc: true
seo:
  title: "" # custom title (optional)
  description: "" # custom description (recommended)
  canonical: "" # custom canonical URL (optional)
  noindex: false # false (default) or true
---

# 使用 ddclient 动态更新 Cloudflare DNS Record 实现 DDNS

#Omnivore

[Read on Omnivore](https://omnivore.app/me/ddclient-cloudflare-dns-record-ddns-ward-chan-18c4017e01e)
[Read Original](https://blog.wardchan.com/posts/use-ddclient-to-automatically-update-cloudflare-dns-record.html)

作者最近在使用一些动态 IP 的 VPS，而这些 VPS 服务商大都因为害怕被攻击而不直接提供 DDNS，而是提供教程让我们自建 DDNS。Cloudflare 动态更新 DNS Record 的方式会稍微麻烦一些，因此大部分 VPS 服务商都不介绍这种方式，这里简单记录一下。

### 安装依赖

确认 libdata-validate-ip-perl 和 socket-ssl-perl 是否已经安装，如果没有安装可以通过以下命令安装。

```
sudo apt install libdata-validate-ip-perl libio-socket-ssl-perl
```

### 下载安装 ddclinent

这里要注意一下，大部分系统通过安装包管理器安装的 ddclinet 都是比较旧的版本，例如目前 Debian 10 通过 apt 安装的是 ddclient v3.8.3，而这个版本并不支持 Cloudflare 相关的功能。因此我们需要从 ddclient 的 Github 下载安装。

通过以下命令从 [Github](https://github.com/ddclient/ddclient/releases) 下载 ddclient 并解压。

```
wget https://github.com/ddclient/ddclient/archive/v3.9.1.tar.gz
tar xzvf v3.9.1.tar.gz
```

进入到解压后的目录并将 ddclient 复制到 /usr/sbin/ 下。

```
cd v3.9.1/
sudo cp ddclient /usr/sbin/
```

### 创建相关目录和配置文件

创建配置文件存放目录 /etc/ddclient 和缓存文件存放目录 /var/cache/ddclient。

```
sudo mkdir /etc/ddclient /var/cache/ddclient
```

创建 ddclient 配置文件。

```
sudo vim /etc/ddclient/ddclient.conf
```

配置文件内容可以参考作者的。

```
daemon=300 # 多久检查一次服务器外网 IP
pid=/var/run/ddclient.pid

protocol=cloudflare
use=web
web=https://ipinfo.io/ip
login=i@email.com # Cloudflare 登录邮箱
password=gH8afpRjgzL3ZYpsCzjKKwDokxsVBuGVdoDPya # CloudFlare API 密钥
zone=wardchan.com # 告诉 ddclient 要更新哪个一级域名下的 DNS Record
hk.wardchan.com, us.wardchan.com # 告诉 ddclient 要根据具体哪个域名的 DNS Record，可以通过逗号连接来更新多个域名
```

### 创建服务并设置开机启动

创建 ddclient 服务保证服务一直运行不中断。

```
sudo cp sample-etc_systemd.service /etc/systemd/system/ddclient.service
sudo systemctl enable ddclient.service
sudo systemctl start ddclient.service
```

可以通过以下命令来验证配置是否正确。

```
ddclient -daemon=0 -debug -verbose -noquiet
```

本文链接：[https://blog.wardchan.com/posts/use-ddclient-to-automatically-update-cloudflare-dns-record.html](https://blog.wardchan.com/posts/use-ddclient-to-automatically-update-cloudflare-dns-record.html)，[参与评论 »](https://blog.wardchan.com/posts/use-ddclient-to-automatically-update-cloudflare-dns-record.html#comments)
