---
title: "Brew相关"
date: 2017-10-15T22:33:25+08:00
draft: false
categories: ["MacOS"]
tags: ["brew"]
---

MacOS下包管理工具brew相关用法记录
<!--more-->

包管理工具

Homebrew

命令：brew

brew安装目录

/usr/local/Cellar

brew安装mysql

``` sh
brew install mysql
```

启动MySQL

    To have launchd start mysql now and restart at login:
    brew services start mysql
    Or, if you don't want/need a background service you can just run:
    mysql.server start

brew安装autojump

``` sh
brew install autojump
```
