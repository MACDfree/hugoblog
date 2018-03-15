---
title: "快速SSH连接工具（自用）"
date: 2018-03-08T22:15:08+08:00
lastmod: 2018-03-08T22:15:08+08:00
draft: false
keywords: ["ssh", "go"]
description: "用go编写的快速SSH连接工具"
tags: ["go", "ssh"]
categories: ["go"]
author: ""

# You can also close(false) or open(true) something for this content.
# P.S. comment can only be closed
comment: false
toc: false
autoCollapseToc: false
postMetaInFooter: true
# You can also define another contentCopyright. e.g. contentCopyright: "This is another copyright."
contentCopyright: ""
reward: true
mathjax: false
mathjaxEnableSingleDollar: false
---

使用go语言编写的快速SSH连接的工具

<!--more-->

之前一直使用XShell作为SSH远程连接工具，但它只对家庭和学生免费使用，如果要在工作中使用是需要收费的。而且最近公司对这一块查得比较严，因此只能找一下替代软件。

第一个想到的就是putty，但却有个不可接受的缺点：无法保存连接信息，每次都需要手动输入用户名和密码。在此说明一下本人的使用场景：部门中大约有七八台测试服务器，偶尔需要登录其中的某一台进去查看一下应用日志获取服务器状态，对于一台服务器，可能几个星期都不去登录。在此场景下，有可能登录输入用户名密码的时间比实际使用的时间还长，这是无法忍受的。（虽然可以配置密钥登录，但觉得在公司服务器上加点自己的东西很别扭）

之后经过一番折腾，在Windows系统中安装了Ubuntu子系统和open-wsl终端工具，配合使用go编写的ssh连接工具满足了自己的需求。

程序的主要功能是管理SSH连接，快速连接远程主机。用法：

``` sh
sshs alias # 登录
sshs add alias -i ip -p port -u username -w password # 添加
sshs rm alias # 删除
sshs ls [alias] # 查看（支持模糊匹配）
```

[源码链接](https://gitee.com/MACDfree/codes/jan2ykrvu8so5tp0lw7c693)