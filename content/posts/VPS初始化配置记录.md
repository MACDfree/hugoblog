---
title: "VPS初始化配置记录"
date: 2018-07-12T23:33:33+08:00
lastmod: 2018-07-12T23:33:33+08:00
draft: false
keywords: ["VPS"]
description: "VPS初始化配置记录"
tags: ["vps", "centos"]
categories: ["linux"]
author: ""

# You can also close(false) or open(true) something for this content.
# P.S. comment can only be closed
comment: false
toc: true
autoCollapseToc: false
postMetaInFooter: true
hiddenFromHomePage: false
# You can also define another contentCopyright. e.g. contentCopyright: "This is another copyright."
contentCopyright: ""
reward: true
mathjax: false
mathjaxEnableSingleDollar: false

flowchartDiagrams:
  enable: false
  options: ""

sequenceDiagrams: 
  enable: false
  options: ""

---

由于之前用的虚拟主机不稳定，丢包率很高，所以不得不重新换了一个主机。换完主机后需要重新配置安装，在此记录一下，方便以后参考。

<!--more-->

# 安装bbr

```bash
wget --no-check-certificate https://github.com/teddysun/across/raw/master/bbr.sh && chmod +x bbr.sh && ./bbr.sh
```

# 安装配置shadowsocks

## 安装

```bash
yum install python-setuptools && easy_install pip
pip install shadowsocks
```

## 配置

``` bash
vim /etc/shadowsocks.json
```

添加如下内容

```json
{
    "server":"ip",
    "server_port":12345,
    "local_address":"127.0.0.1",
    "local_port":1080,
    "password":"password",
    "timeout":300,
    "method":"aes-256-cfb",
    "fast_open":false
}
```

## 启动

```bash
ssserver -c /etc/shadowsocks.json -d start
```

# 配置iptables

## 关闭防火墙

```bash
systemctl stop firewalld.service
systemctl disable firewalld.service
```

## 安装iptables

```bash
yum install -y iptables-services
systemctl enable iptables.service
systemctl start iptables.service
```

## 安装使用net-tools

```bash
yum install net-tools
netstat -apn | grep 8989
```

## 添加规则

```bash
iptables -I INPUT 5 -p tcp --dport 8989 -j ACCEPT
iptables -I INPUT 6 -p udp --dport 8989 -j ACCEPT
service iptables save
```

# 部署JetBrainsLicenseServer

由于代码使用Go编写，所以首先需要安装Go环境。（有时间考虑编译好各平台的可执行文件，方便部署）

```bash
git clone https://gitee.com/MACDfree/JetBrainsLicenseServerforGo.git
go build main.go
nohup ./main -addr :1234 &
```

还需要将端口配置在iptables中
