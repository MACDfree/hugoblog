---
title: "Web.xml记录"
date: 2017-10-15T21:55:17+08:00
draft: false
categories: ["Java"]
tags: ["web.xml"]
---

web.xml相关知识点记录
<!--more-->

# Filter

需要实现javax.servlet.Filter接口。
在Web应用启动时初始化（仅初始化一次），随Web应用停止而销毁。会拦截每次客户端的请求。

# Listener

需要实现javax.servlet.ServletContextListener接口。
在Web应用启动时初始化（仅初始一次），随Web应用停止而销毁。
Web应用启动时需要会读取web.xml中context-param节点的内容以key-value的形式放入ServletContext中，在Listener中可以通过ServletContextEvent参数的getServletContext方法获取ServletContext。

由上可知，Filter如其名，对每次客户端请求做处理（过滤）；Listener则是作为一个初始化的功能。

# web.xml加载流程

context-param > listener > filter > sevlet > spring...

# classpath和classpath*的区别

classpath：只在当前的classpath下查找

classpath*：不仅在当前classpath下查找，而且会在jar包的classes中去查找
