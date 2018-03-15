---
title: "Keytool使用"
date: 2017-10-15T21:51:56+08:00
draft: false
categories: ["Java"]
tags: ["keytool"]
---

JDK中证书管理工具keytool的使用
<!--more-->

keytool工具所在路径：`jdkpath/bin/keytool[.exe]`

导入密钥库

``` sh
keytool -importcert -file demo.crt -keystore demo.jks -alias demo
```

删除指定密钥库中的证书

``` sh
keytool -delete -alias demo -keystore demo.jks
```

列出密钥库中的所有证书

``` sh
keytool -list -keystore demo.jks
```
