---
title: "Linux常用命令"
date: 2017-10-15T22:23:08+08:00
draft: false
categories: ["Linux"]
tags: []
---

Linux常用命令记录
<!--more-->

# 查看端口占用

``` bash
netstat -apn | grep 80
```

# 解压缩文件tar.gz

``` bash
tar -zxvf filename
```

# 通过ssh上传下载文件

``` bash
scp username@servername:/path/filename /var/www/local_dir  （从服务器下载文件）
scp /path/filename username@servername:/path （上传文件至服务器）
scp -r username@servername:/var/www/remote_dir/ /var/www/local_dir（从服务器下载文件夹）
scp  -r local_dir username@servername:remote_dir （上传文件夹至服务器）
```

# 修改服务器时间

``` bash
date -s "2017-07-12 12:22:00"
```

# iptables

``` bash
# 查看
iptables -L -n --line-number
# 添加规则
iptables -A INPUT -p tcp --dport 22 -j ACCEPT
# 插入规则
iptables -I INPUT 2 -p tcp --dport 22 -j ACCEPT
# 删除规则
iptables -D INPUT 2
# 保存规则
service iptables save
```
