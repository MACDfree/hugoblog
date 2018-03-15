---
title: "Shiro学习记录"
date: 2017-10-15T22:02:11+08:00
draft: false
categories: ["Java"]
tags: ["shiro"]
---

安全框架Shiro学习记录
<!--more-->

Authentication：身份认证

Authorization：授权

身份认证流程：

Subject -> Security Manager -> Authenticator -> AuthenticationStrategy -> Realm