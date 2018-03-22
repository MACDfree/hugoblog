---
title: "Axis调用CXF发布的WebService遇到的问题"
date: 2018-03-22T12:40:50+08:00
lastmod: 2018-03-22T12:40:50+08:00
draft: true
keywords: ["axis", "cxf", "string[]", "WebService"]
description: "Axis调用CXF发布的WebService遇到的问题"
tags: ["axis", "cxf", "string[]"]
categories: ["java"]
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

最近遇到一个**Axis**调用**CXF**发布的**WebService**时报错的问题，在此记录一下问题解决的过程。

<!--more-->

客户使用**Axis**调用我方使用**CXF**发布的**WebService**报错。起初，报错信息为找不到方法，如下图所示。

经过查找，发现是需要在方法上添加注解，显式注明namespace。同时，也注意到方法参数及返回值也都要加上类似注解，如下图所示。

修改完成后，本地使用**Axis**测试调用成功，故更新至测试系统与客户联调，发现还是报错，但报错信息改为类型转换错误，如下图所示。

根据错误信息查找多次后仍未解决，此时，只好询问客户是否可以提供调用代码。获取调用代码后，发现确实调用代码有所不同，如下图所示。

使用客户提供的调用代码测试确实调用不成功，但**WebService**确实是按照客户提供的文档发布的，同时发现**WebService**端返回值类型为`String[]`，但调用方接收到的是`List`类型，所以导致了转换出错。由此怀疑是不是客户调用方法有问题，但沟通后反馈说其他系统对接过，没有问题，此时问题陷入了死胡同。

面对这种问题，需要看透问题的本质，**WebService**的调用依靠的是**WSDL**描述调用属性，既然我们调用不成功，会不会是生成的**WSDL**有问题呢？按照这个思路，咨询客户获得了一份之前对接成功的系统的**WSDL**。经过比对，果然发现返回值类型并不是文档中写明的`String[]`，而是`ArrayOfString`，经过搜索，很快就找到了`ArrayOfString`的源码，修改代码后，本地测试通过，再和客户联调也验证通过，问题至此解决。

但疑问还没有消除，根据`ArrayofString`的源码，其实也只是封装了一个`List<String>`，为什么使用**Axis**调用后的返回值可以转换成`String[]`？希望了解的人告知一二。