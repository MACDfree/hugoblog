---
title: "Pip安装常见问题"
date: 2017-10-16T20:17:30+08:00
draft: false
categories: ["Python"]
tags: ["pip"]
---

报错：

    fatal error: Python.h: No such file or directory

缺少python-dev开发包

<!--more-->

安装：

``` sh
yum install python-devel
yum install python34-devel
```

