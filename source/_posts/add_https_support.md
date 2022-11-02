---
title: 给你的站点添加https访问支持
author: 小鱼
date: 2022-10-18 17:02:40
tags: [技术分享,web]
cover: /image/add_https_support/https.jpg
---

HTTPS( 超文本传输安全协议)，多出来的S，就是`Secure`，比HTTP多了一层安全保障。

## 准备工作

升级到https需要获得ssl证书，做这些之前，你必须完全满足以下几个条件：

- 知道终端命令如何使用
- 能够使用ssh连接到服务器
- 有一个可以通过http访问的站点，且是在线状态
- 80端口已经开放
- 站点托管在要申请ssl证书的服务器上
- 对服务器具有root访问权限

## 申请证书

确保自己满足以上条件后，我们就可以开始下一步了。此文使用[Let’s Encrypt](https://letsencrypt.org/)的[Certbot](https://certbot.eff.org/)工具为运行Centos 7操作系统且安装了[nginx代理](https://docs.nginx.com/?_ga=2.132174473.629001847.1666085257-300779752.1666085257)的服务器申请证书。

### SSH 进入服务器

以具有 sudo 权限的用户身份通过 SSH 连接到运行部署了 HTTP 网站的服务器。

```shell
ssh root@ip_address
# ip_address为你自己的服务器IP地址
```

### 安装 snapd

[根据服务器操作系统选择安装snapd](https://snapcraft.io/docs/installing-snapd/)，本文以CentOS 7为例。

使用以下命令将 EPEL 存储库添加到 CentOS 7 系统：

```bash
sudo yum install epel-release
```

将 EPEL 存储库添加到CentOS后，只需安装*snapd*软件包：

```bash
sudo yum install snapd
```

使用systemctl管理snap：

```bash
sudo systemctl enable --now snapd.socket
```

给snap创建软连接：

```bash
sudo ln -s /var/lib/snapd/snap /snap
```

确保您的 snapd 版本是最新的

在服务器上的命令行执行以下指令，确保拥有最新版本的`snapd`：

```bash
sudo snap install core；sudo snap refresh core
```

### 安装 Certbot

在服务器上的命令行上运行此命令以安装 Certbot：

```bash
sudo snap install --classic certbot
```

准备 Certbot 命令

在服务器上的命令行执行以下指令，保证`certbot`命令可以运行：

```bash
sudo ln -s /snap/bin/certbot /usr/bin/certbot
```

### 安装ssl证书

在服务器的命令行运行此命令以获取证书并让 Certbot `自动编辑您的 nginx 配置`以提供服务:

```bash
sudo certbot --nginx
```

证书安装阶段会需要用户输入进行选择，y/n,输入y同意并继续，在列出的域名中输入域名前面的`序号`选择为此域名申请证书。

### 测试自动续订

通过运行以下命令来测试证书的自动续订，certbot的续订会在证书到期前自动进行：

```bash
sudo certbot renew --dry-run
```

更新 certbot 的命令安装在以下位置之一：

- `/etc/crontab/`
- `/etc/cron.*/*`
- `systemctl list-timers`

## 使用https访问你的域名

如果一路上没有遇到错误，或者说遇到的错误已经解决，在浏览器地址栏中输入`https://yourwebsite.com/`访问你的站点测试https是否成功部署。

### 强制https访问

如果需要将每次的访问请求强制转发为https，请访问此文:[强制https访问](http://afish.org/2022/09/25/force-https/)
