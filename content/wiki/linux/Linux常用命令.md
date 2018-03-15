---
title: "Linux常用命令"
date: 2017-10-15T22:23:08+08:00
draft: false
categories: ["Linux"]
tags: []
---

Linux常用命令记录
<!--more-->

查看端口占用

``` sh
netstat -apn | grep 80
```

解压缩文件tar.gz

``` sh
tar -zxvf filename
```

通过ssh上传下载文件

``` sh
scp username@servername:/path/filename /var/www/local_dir  （从服务器下载文件）
scp /path/filename username@servername:/path （上传文件至服务器）
scp -r username@servername:/var/www/remote_dir/ /var/www/local_dir（从服务器下载文件夹）
scp  -r local_dir username@servername:remote_dir （上传文件夹至服务器）
```

修改服务器时间

``` sh
date -s "2017-07-12 12:22:00"
```
