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
curl -C - -O url
```

其中`-C -`表示让curl自动判断偏移位置进行断点续传下载