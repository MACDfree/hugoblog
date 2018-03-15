---
title: "Curl使用"
date: 2017-10-15T22:27:35+08:00
draft: false
categories: ["Linux"]
tags: ["curl"]
---

curl用法记录
<!--more-->

查看请求网址返回信息

``` sh
curl -i url
```

下载文件

``` sh
curl -O url
```

断点续传

``` sh
curl -O url
```

下载过程中中断下载

``` sh
curl -C -O url
```

只用-C参数继续对该文件下载