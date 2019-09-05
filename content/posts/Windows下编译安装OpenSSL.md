---
title: "Windows下编译安装OpenSSL"
date: 2019-09-05T16:19:05+08:00
lastmod: 2019-09-05T16:19:05+08:00
draft: false
keywords: []
description: "Windows下编译安装OpenSSL"
tags: ["OpenSSL"]
categories: ["OpenSSL"]
author: ""

# You can also close(false) or open(true) something for this content.
# P.S. comment can only be closed
comment: false
toc: false
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

最近需要使用老版本的OpenSSL库进行开发，但网上搜索了一圈并没有编译好的版本。无奈只能自己搭建编译环境进行编译安装。

<!--more-->

# 环境准备

1. VS2010，由于工作中有版本限制，所以只能使用VS2010。正常可以使用更新的版本

2. ActivePerl，由于OpenSSL编译需要Perl环境，所以需要安装

3. NASM，汇编器，将汇编代码编译成可执行程序

4. OpenSSL源码

# 编译安装（32位）

1. 打开VS2010命令行，地址：`C:\ProgramData\Microsoft\Windows\Start Menu\Programs\Microsoft Visual Studio 2010\Visual Studio Tools`

2. 执行`vcvarsall.bat`，进行环境变量设置

3. 切换至OpenSSL源码目录，执行`perl Configure VC-WIN32 --prefix=c:\openssl\x86`，`VC-WIN32`表示编译32位版本，`prefix`为设置最后的安装路径

4. 在OpenSSL目录下，执行`ms\do_nasm`

5. 在OpenSSL目录下，执行`nmake -f ms\ntdll.mak`

6. 在OpenSSL目录下，执行`nmake -f ms\ntdll.mak test`，运行单元测试，测试通过表示编译成功

7. 在OpenSSL目录下，执行`nmake -f ms\ntdll.mak install`，会将编译好的文件复制到第三步指定的目录下

8. 在OpenSSL目录下，执行`nmake -f ms\ntdll.mak clean`，进行清理，方便后续再编译64位版本

# 编译安装（64位）

1. 打开VS2010命令行，地址：`C:\ProgramData\Microsoft\Windows\Start Menu\Programs\Microsoft Visual Studio 2010\Visual Studio Tools`

2. 执行`vcvarsall.bat`，进行环境变量设置

3. 切换至OpenSSL源码目录，执行`perl Configure VC-WIN64A --prefix=c:\openssl\x64`，`VC-WIN64A`表示编译64位版本，`prefix`为设置最后的安装路径

4. 在OpenSSL目录下，执行`ms\do_win64a`

5. 在OpenSSL目录下，执行`nmake -f ms\ntdll.mak`

6. 在OpenSSL目录下，执行`nmake -f ms\ntdll.mak test`，运行单元测试，测试通过表示编译成功

7. 在OpenSSL目录下，执行`nmake -f ms\ntdll.mak install`，会将编译好的文件复制到第三步指定的目录下
