---
title: "MySQL修改连接时间"
date: 2017-10-15T23:07:49+08:00
draft: false
categories: ["MySQL"]
tags: []
---

Windows下修改`my.ini`文件，在mysqld节点下增加如下配置并重启
<!--more-->

``` ini
wait_timeout=388000
interactive-timeout=388000
```
