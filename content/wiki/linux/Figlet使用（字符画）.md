---
title: "Figlet使用（字符画）"
date: 2017-10-15T22:17:44+08:00
draft: false
categories: ["Linux"]
tags: ["figlet", "字符画"]
---

字符画工具Figlet的安装和使用
<!--more-->

下载figlet源码包

解压缩

``` sh
tar -zxvf 
```

安装GCC

``` sh
yum install gcc
```

编译

``` sh
make figlet
```

执行

``` sh
./figlet HELLO -f fonts/slant.flf
```