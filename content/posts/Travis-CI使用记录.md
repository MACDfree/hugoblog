---
title: "Travis CI使用记录"
date: 2018-03-15T21:40:29+08:00
lastmod: 2018-03-15T21:40:29+08:00
draft: true
keywords: ["Travis CI"]
description: ""
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

<!--more-->

install 安装依赖的脚本ceshi 
如果有多个脚本，只要有脚本错误就立即停止构建
script 运行脚本
如果有多个脚本，即使有错误，也会运行下去，但最后结果显示失败