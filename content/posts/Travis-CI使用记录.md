---
title: "Travis CI使用记录"
date: 2018-03-15T21:40:29+08:00
lastmod: 2018-03-15T21:40:29+08:00
draft: true
keywords: ["Travis CI"]
description: "Travis CI使用记录"
tags: ["Travis CI"]
categories: ["持续集成"]
author: ""

# You can also close(false) or open(true) something for this content.
# P.S. comment can only be closed
comment: false
toc: true
autoCollapseToc: false
postMetaInFooter: true
# You can also define another contentCopyright. e.g. contentCopyright: "This is another copyright."
contentCopyright: ""
reward: true
mathjax: false
mathjaxEnableSingleDollar: false
---

最近有空使用了一下Travis CI持续集成工具，在此记录一下备忘。

<!--more-->

# 概述

公司使用的持续集成工具为**Jenkins**，使用Web可视化界面进行配置，操作简单易上手。而 **Travis CI** 通过**yaml**文件（`.travis.yml`）进行配置，有一点上手难度，但同时提高了灵活度。

从使用下来的感受来说，**Travis CI** 就是根据配置执行一系列脚本，实现代码编译、单元测试、打包和发布的功能。

# 从 `.travis.yml` 说起

Travis 要求项目根目录下必须包含 `.travis.yml` 文件，文件中包含了需要执行操作的配置。从文件后缀名可以看出，使用的是 **yaml** 文件格式，下面看一个 `.travis.yml` 的示例：

```yaml
language: go
sudo: false
go:
  - 1.9.x
deploy:
  provider: releases
  api_key: $GITHUB_OAUTH_TOKEN
  file:
    - "bin/sshs-${TRAVIS_TAG}-linux-amd64"
    - "bin/sshs-${TRAVIS_TAG}-darwin-amd64"
  skip_cleanup: true
  on:
    tags: true
install:
  - go get -d -t -v ./...
script:
  - chmod +x build.sh
  - ./build.sh
notifications:
  email:
    on_failure: always
    on_success: never
```

以上是一个使用Go语言的配置，`language` 配置需要构建的语言环境；`go` 配置使用的go语言版本；`deploy` 配置构建好后文件发布的参数，在此不深入展开，具体可参考官方文档；`install` 配置项目依赖需要执行的指令，可配置多条指令；`script` 配置构建指令，同样可以配置多条指令；`notifications` 配置通知方式，具体配置可参考官方文档。

对于 `.travis.yml` 文件，**Travis** 的执行顺序为：`before_install` -> `install` -> `before_script` -> `script` -> `deploy`。

# 项目的构建流程

![travislog](http://ocd8m6zlz.bkt.clouddn.com/travislog.png)