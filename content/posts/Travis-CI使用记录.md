---
title: "Travis CI使用记录"
date: 2018-03-15T21:40:29+08:00
lastmod: 2018-03-22T22:26:29+08:00
draft: false
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

以上是一个使用Go语言的配置，`language` 配置需要构建的语言环境；`go` 配置使用的go语言版本；`deploy` 配置构建好后文件发布的参数，在此不深入展开，具体可参考[官方文档](https://docs.travis-ci.com/user/deployment/releases/)；`install` 配置项目依赖需要执行的指令，可配置多条指令；`script` 配置构建指令，同样可以配置多条指令；`notifications` 配置通知方式，具体配置可参考[官方文档](https://docs.travis-ci.com/user/notifications)。

对于 `.travis.yml` 文件，**Travis** 的执行顺序为：`before_install` -> `install` -> `before_script` -> `script` -> `deploy`。

# 项目的构建流程

下图是使用**Travis CI**构建Go项目的输出日志

![travislog](http://ocd8m6zlz.bkt.clouddn.com/travislog.png)

由图可知，构建任务可分为：

1. 拉取**GitHub**仓库代码
2. 设置相关环境变量
3. 将代码同步至指定的目录下（此处应该和具体语言环境相关）
4. 切换至代码所在目录
5. 执行`install`中的相关指令（如果之前配置了`before_install`，则先执行`before_install`中的指令）
6. 执行`script`中的指令（如果之前配置了`before_script`，则先执行`before_script`中的指令）
7. 最后执行发布操作

如此梳理过构建流程后，可以发现：**Travis CI**只是提供里构建所需的系统环境和框架，并且开放不同阶段的钩子，具体构建操作可由用户通过`.travis.yml`自定义配置，提高了可玩性，但同时也对使用者的**bash**脚本编写能力有了一定的要求。

最后说一些题外话，之前本人的博客使用**GitHub Pages + hugo** 搭建。同时原始md文件存放在另一个仓库中，为了实现只要提交md文件，系统自动生成静态页面并发布至**GitHub**的需求，特地使用**Go**编写了一个**BlogCI**的服务程序，[仓库地址](https://github.com/MACDfree/BlogCI)，大体思路是：当有md文件提交时，使用**webhooks**发送http请求至**BlogCI**，**BlogCI**接收到请求后在本地拉取最新的md文件，调用**hugo**命令生成静态页面，最后发布至**GitHub Pages**。上述流程看着是不是很眼熟？这不就是**Travis CI**做的事情吗。所以，现在可以摒弃之前丑陋的实现，直接使用**Travis CI**实现博客的自动构建了。详细教程可参考[官方文档](https://docs.travis-ci.com/user/deployment/pages/)。