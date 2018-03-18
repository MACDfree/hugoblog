---
title: "快速SSH连接工具（自用）"
date: 2018-03-08T22:15:08+08:00
lastmod: 2018-03-18T18:28:08+08:00
draft: false
keywords: ["ssh", "go", "ssh连接管理工具"]
description: "用go编写的快速SSH连接工具"
tags: ["go", "ssh"]
categories: ["go"]
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

使用go语言编写的SSH连接管理工具

<!--more-->

# 原因

之前一直使用XShell作为SSH远程连接工具，但它只对家庭和学生免费使用，如果要在工作中使用是需要收费的。而且最近公司对这一块查得比较严，因此只能找一下替代软件。

第一个想到的就是putty，但却有个不可接受的缺点：无法保存连接信息，每次都需要手动输入用户名和密码。在此说明一下本人的使用场景：部门中大约有七八台测试服务器，偶尔需要登录其中的某一台进去查看一下应用日志获取服务器状态，对于一台服务器，可能几个星期都不去登录。在此场景下，有可能登录输入用户名密码的时间比实际使用的时间还长，这是无法忍受的。（虽然可以配置密钥登录，但觉得在公司服务器上加点自己的东西很别扭）

# 折腾

## 准备

几番对比之后，本人选择了在Windows中安装Ubuntu子系统，配合open-wsl终端工具满足ssh连接的需求。但问题还是没有解决，所以动手写了个可以管理ssh session的小工具，供自己使用。

## 前期规划

起初想直接使用bash脚本实现，但奈何底子太差放弃了。然后果断选择了用go实现（因为一直想用go写点东西）。

选择完语言后，先做个前期规划，希望实现的效果如下：

``` bash
sshs alias # 登录
sshs add alias -i ip -p port -u username -w password # 添加
sshs rm alias # 删除
sshs ls [alias] # 查看（支持模糊匹配）
```

接着考虑数据存储格式，选用yaml格式：

``` yaml
alias1:
  ip: "192.168.123.123"
  port: 22
  username: "root"
  password: "123456"
```

文件路径：`~/.sshs.yml`。

## 代码实现

需要实现的功能主要包括：解析yaml文件管理（增删查）ssh session、ssh连接远程主机。其中解析yaml使用`gopkg.in/yaml.v2`实现，ssh连接使用`golang.org/x/crypto/ssh`实现。另外，由于go标准库中的flags包不支持sub commend，所以使用了`gopkg.in/urfave/cli.v1`实现命令解析功能。

最后，附上源码地址[sshs-ssh连接管理工具](https://github.com/MACDfree/sshs)