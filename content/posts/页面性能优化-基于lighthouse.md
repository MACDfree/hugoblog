---
title: "页面性能优化-基于lighthouse"
date: 2018-03-07T14:13:30+08:00
lastmod: 2018-03-08T11:41:30+08:00
draft: false
keywords: ["前端性能优化", "lighthouse"]
description: "根据Lighthouse进行页面性能优化"
tags: ["前端性能优化", "lighthouse"]
categories: ["html"]
author: ""

# You can also close(false) or open(true) something for this content.
# P.S. comment can only be closed
comment: true
toc: true
autoCollapseToc: false
postMetaInFooter: true
# You can also define another contentCopyright. e.g. contentCopyright: "This is another copyright."
contentCopyright: ""
# 打赏功能
reward: true
mathjax: false
mathjaxEnableSingleDollar: false
---

最近使用GoogleChrome的Lighthouse插件对公司的登录页面进行了检测优化，在此记录一下具体优化过程。
<!--more-->

# 前期准备

## Lighthouse安装

使用Lighthouse有两种方式：Chrome扩展程序或命令行工具，本文只介绍以Chrome扩展程序方式使用Lighthouse。

* 适用的Chrome版本：52或更高版本。
* 安装[Lighthouse扩展程序](https://chrome.google.com/webstore/detail/lighthouse/blipmdconlkpinefehnmjammfjpmpbjk)。

## Lighthouse使用

Lighthouse扩展程序安装完成后，打开需要检测的页面，点击右上角的插件图标再点击 “Generate report” 按钮开始进行检测。

![Lighthouse插件](https://blog-1254016481.cos.ap-shanghai.myqcloud.com/Lighthouse插件.png)

检测完成后，插件会自动打开检测报告页面，如下图所示：

![旧页面性能截图](https://blog-1254016481.cos.ap-shanghai.myqcloud.com/旧页面性能截图.png)

上图是测试系统登录页面的性能得分，可以看到，分数极低。下面就对照检测点，进行修改。

# 检测点修改

## Enable text compression

![gzip压缩](https://blog-1254016481.cos.ap-shanghai.myqcloud.com/gzip压缩.png)

点开详细信息，可以看到测试系统没有使用gzip压缩。

由于测试系统使用Nginx代理，所以直接添加Nginx配置，如下：

``` nginx
gzip on;
gzip_min_length 3k;
gzip_buffers  4 4k;
gzip_comp_level 3;
gzip_types text/plain application/x-javascript application/javascript text/css application/xml text/javascript application/x-httpd-php image/jpeg image/gif image/png;
gzip_vary on;
```

* gzip_min_length *length*;

    只有当响应头中的`Content-Length`大于`length`时才进行压缩，实际测试中发现调整到 3k 比较合适

* gzip_buffers *number size*;

    设置用于压缩响应数据的内存大小，默认size等于一个内存页的大小，在Linux中可以使用命令`getconf PAGESIZE`进行查看

* gzip_comp_level *level*;

    设置压缩级别，~~按照我的理解，级别越高表示压缩率越高体积越小，但同时压缩效率也越低~~。

* gzip_types *mime-type ...*;

    设置指定哪些MIME type需要被压缩，值`"*"`表示匹配任意MIME type。此外，`"text/html"`类型总是被压缩的。

* gzip_vary *on | off*;

    当启用`gzip`，`gzip_static` 或 `gunzip`是是否在响应头中插入`Vary: Accept-Encoding`属性。

## Serve images in next-gen formats

![图片过大](https://blog-1254016481.cos.ap-shanghai.myqcloud.com/图片文件过大.png)

检查点建议使用新的图片格式以提高压缩率，减小图片体积。针对此点，考虑到兼容性问题，本人并没有使用建议的图片格式，而是将png格式转成了jpg格式，从原来的 341 KB 缩小到 55.7 KB，虽然png是无损压缩，但对于登录页大背景图片来说jpg才是推荐的图片格式。

## Reduce render-blocking scripts & stylesheets

![阻塞的js和css](https://blog-1254016481.cos.ap-shanghai.myqcloud.com/阻塞的js和css.png)

检测点中列出的js和css都是在`<head>`标签中，浏览器会等待这些css和js都加载完毕后再渲染页面，从而导致页面加载速度下降。本人采取的做法是将真正需要在页面渲染前加载的部分js和css使用`<script>`和`<style>`标签写在页面上，这样可以减少页面渲染前的资源加载。

## Unused CSS rules

![无用的css](https://blog-1254016481.cos.ap-shanghai.myqcloud.com/无用的css.png)

这个检测点比较简单，需要注意的是列出的css并不是真的都是没有用到的，需要人工核查。

# 总结

通过检测点的修改，我们大致可以明确提升页面性能的几个目标：

1. 减小数据大小
2. 减少资源加载数量
3. 设法降低页面首次渲染完成的时间

其中前两点可以认为是为第三点服务的，但要实现第三点，不仅需要完成前两点，还需要针对具体页面，分析梳理页面关键资源，对于非关键资源采用延迟加载，以提高页面加载速度。

另外，还可以使用HTTP/2的server push功能在一次请求中返回多个资源文件，从而减少HTTP请求次数，提高页面性能。