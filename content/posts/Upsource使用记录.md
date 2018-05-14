---
title: "Upsource使用记录"
date: 2018-05-12T12:39:45+08:00
lastmod: 2018-05-12T12:39:45+08:00
draft: false
keywords: ["upsource"]
description: "Upsource使用记录"
tags: ["upsource"]
categories: ["java"]
author: ""

# You can also close(false) or open(true) something for this content.
# P.S. comment can only be closed
comment: false
toc: true
autoCollapseToc: false
postMetaInFooter: true
hiddenFromHomePage: false
# You can also define another contentCopyright. e.g. contentCopyright: "This is another copyright."
contentCopyright: ""
reward: true
mathjax: false
mathjaxEnableSingleDollar: false

flowchartDiagrams:
  enable: false
  options: ""

sequenceDiagrams: 
  enable: false
  options: ""

---

关于Upsource的安装、配置、使用记录

<!--more-->

# 安装

## 推荐服务器配置

* 8GB及以上内存
* 64位操作系统
* 足够的硬盘

## 安装配置

安装包下载地址：[https://www.jetbrains.com/upsource/download](https://www.jetbrains.com/upsource/download)

将下载好的安装包解压缩到安装目录即完成安装。

在启动前，需要进行一些细微配置：

* 禁用冲突的软件（Windows）

   如果您在Windows上运行Upsource，请为Upsource根目录禁用所有反病毒，Windows Defender和Windows Search服务，因为它们可能与Upsource进程冲突。

* 调整资源限制（Linux）

   如果您在Linux上运行Upsource，资源限制不足可能会导致大量错误。
   为了防止这种情况，我们建议设置：

  * 最大打开文件数调整为100000
  * 内存锁定和地址空间限制调整为无限制
  * 进程数调整为32768

  具体配置为在`/etc/security/limits.conf`文件中添加如下配置：

   ```conf
   * - memlock unlimited
   * - nofile 100000
   * - nproc 32768
   * - as unlimited
   ```

配置完成后，进入`<upsource_home>/bin`目录，执行`upsource.bat start`启动Upsource，执行`upsource.bat service install /runAsSystem`可以将Upsource注册为Windows服务，执行`upsource.bat stop`停止Upsource。

第一次启动Upsource时会自动打开默认浏览器跳转至欢迎界面

![welcone](http://ocd8m6zlz.bkt.clouddn.com/upsourcesetup.png)

点击`Set up`进入设置页面

![settings](http://ocd8m6zlz.bkt.clouddn.com/upsourcesettings.png)

`Base URL`为Upsource访问地址，可以配置为IP+端口号的形式，如`http://192.168.108.68:8880`；`Application Listen Port`为监听端口，需要避免与其他应用冲突。

![hugconfig](http://ocd8m6zlz.bkt.clouddn.com/hubconfig.png)

默认选择`Use Built-in Hub`，设置admin账号的密码。

![license](http://ocd8m6zlz.bkt.clouddn.com/license.png)

默认选择免费授权，支持admin+guest+8users，点击finish完成设置。

![starting](http://ocd8m6zlz.bkt.clouddn.com/starting.png)

等待一段时间配置完成后自动跳转首页

![success](http://ocd8m6zlz.bkt.clouddn.com/success.png)

# 项目管理

用admin账号登录Upsource，按下图操作进行项目新建

![administration](http://ocd8m6zlz.bkt.clouddn.com/admin.png)

![createproject](http://ocd8m6zlz.bkt.clouddn.com/createproject.png)

![projectinfo](http://ocd8m6zlz.bkt.clouddn.com/projectinfo.png)

1. 项目名称
2. 项目ID，默认与项目名称相同
3. 代码评审ID，会根据项目名称自动生成
4. 版本控制工具，这里使用SVN
5. 代码仓库地址
6. 认证方法，这里选择用户名密码认证
7. SVN用户名
8. SVN密码
9. 可配置多个分支，例如：

   ```conf
   +:trunk
   +:branches/*
   ```

   表示包含主干和所有分支
10. 检查间隔，300秒表示每个5分钟检查一次SVN是否有新的提交
11. 自动为此项目创建`Code Viewers`、`Developers`和`Project Admins`用户组

设置完成后点击`Create project`按钮进行项目新增初始化。

# 用户/角色管理

目前为止，我们只有一个admin账号和一个不需要登录的guest账号。为了代码评审的正常进行需要为开发小组成员新增各自的账号并赋予相应的权限。

![user](http://ocd8m6zlz.bkt.clouddn.com/upsourceuser.png)

![new user](http://ocd8m6zlz.bkt.clouddn.com/newuser.png)

如上图所示，填写需要的信息后即可新建用户。建议用户名统一和SVN账号相同，便于代码评审时SVN提交记录和用户对应。

用户新建完成后需要赋予相应的权限。一种简便的方式是通过将用户添加入相应的用户组来进行授权。当新建项目时，默认会在改项目下创建`Code Viewers`、`Developers`和`Project Admins`三个用户组，分别对应此项目的`code-viewer`、`developer`和`project-admin`权限。实际操作中开发小组长同时加入`Code Viewers`、`Developers`和`Project Admins`三个用户组，其他成员加入`Code Viewers`和`Developers`用户组。

![add to group](http://ocd8m6zlz.bkt.clouddn.com/addtogroup.png)

# 代码评审

![project index](http://ocd8m6zlz.bkt.clouddn.com/projectindex.png)

![project home](http://ocd8m6zlz.bkt.clouddn.com/projecthome.png)

1. 快捷链接，从左到右依次为：
   * 代码浏览，列出工程结构，查看代码
   * 代码审查列表
   * 分支查看，如果有多个分支，可以进行切换查看
   * 可视化分析，包括提交活跃度、代码分布、文件历史、代码审查统计和审查人员关系等
2. 版本搜索，可以根据作者、提交时间等进行搜索
3. 时间轴，按照时间和分支展示提交记录
4. 展示每次提交的代码改动，并且可以对代码进行备注，提出自己的看法
5. 针对每次提交都可以创建Code Review，或者加入已有的Code Review
6. 展示与自己相关的消息，例如其他对你提交的代码添加了备注并at了你，此处就会展示此消息，并可进行回复

![code review](http://ocd8m6zlz.bkt.clouddn.com/codereview.png)

1. 修改`Code Review`名字，实际操作中一般设置为评审日期
2. 对当前`Code Review`进行操作，依次为：
   * `Accept`，接受当前Code Review，当与此次Code Review关联的修改都没有问题时点击此按钮
   * `Raise concern`
   * `Close review`，关闭当前Code Review，当和此次Code Review关联的修改点都修改完毕后可点击此按钮
   * `Remove review`，删除此次review，一般是删除无用的Code Review
3. 设置代码审查中的相关人员
4. 与此次Code Review相关的代码修改
5. 针对代码修改提出的备注
