---
title: "开发环境准备 VSCode"
date: 2017-10-15T21:42:59+08:00
draft: false
categories: ["Go"]
tags: ["vscode"]
---

使用`VSCode`搭建`Go`开发环境
<!--more-->

1. 下载Go语言zip包，解压缩到任意目录

2. 配置环境变量，PATH中添加go/bin路径，新增GOROOT环境变量，值为go目录

3. 安装VSCode，安装Go插件

4. 新建gotool文件夹存放Go插件依赖的工具，使用VSCode打开此文件夹，新增工作区配置：`go.toolsGopath`和`go.gopath`（`go.gopath`可以统一配置为：`${workspaceRoot};C:\\path\\golib`）

5. 执行安装Go工具命令