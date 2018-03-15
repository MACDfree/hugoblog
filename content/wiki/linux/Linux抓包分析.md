---
title: "Linux抓包分析"
date: 2017-10-15T22:07:57+08:00
draft: false
categories: ["Linux"]
tags: ["抓包", "tcpdump", "wireshark"]
---

使用tcpdump+wireshark工具组合，tcpdump进行再Linux环境下进行抓包并存储为cap文件，wireshark进行分析。
<!--more-->

抓包命令

``` sh
tcpdump -nnA 'port 8080' -w package.cap
```

``` sh
tcpdump [-i interface] -nnAX 'expression' -w file
-i：指定监听的网卡
-nn：以ip和port的方式显示源朱建和目的主机，而不是主机名和服务
-A：以ASCII的方式显示数据包
-X：以16进制和ASCII方式显示
expression：表达式
```