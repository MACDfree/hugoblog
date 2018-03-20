---
title: "Go命令记录"
date: 2017-11-07T22:18:08+08:00
lastmod: 2018-03-20T10:52:57+08:00
draft: false
categories: ["Go"]
tags: []
---

`Go`相关命令记录

<!--more-->

# 交叉编译

``` sh
CGO_ENABLED=0 GOOS=linux GOARCH=amd64 go install
CGO_ENABLED=0 GOOS=windows GOARCH=amd64 go install
```

其中

GOOS: darwin、freebsd、linux、windows

GOARCH: 386、amd64、arm

注意，上述命令只能在linux中执行，如果是Windows系统，需要分别设置环境变量，PowerShell中类似于

``` powershell
$env:CGO_ENABLED=0
$env:GOOS="linux"
$env:GOARCH="amd64"
go install ...
```

# 编译目录下所有包

``` sh
go install ...
go install /tmp/...
```

# 安装依赖

``` sh
go get -t ./...
```

其中`-t`参数表示下载安装测试代码中的依赖