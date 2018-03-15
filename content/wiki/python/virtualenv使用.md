---
title: "Virtualenv使用"
date: 2017-10-16T20:15:58+08:00
draft: false
categories: ["Python"]
tags: ["virtualenv"]
---

#### 新建虚拟环境（指定解释器）

``` sh
virtualenv -p /usr/bin/python2.7 venv
```

<!--more-->

#### 激活虚拟环境

``` sh
source venv/bin/activate
```

#### 退出虚拟环境

``` sh
deactivate
```