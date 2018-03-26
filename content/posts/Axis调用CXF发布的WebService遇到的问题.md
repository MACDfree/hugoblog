---
title: "Axis调用CXF发布的WebService遇到的问题"
date: 2018-03-22T12:40:50+08:00
lastmod: 2018-03-23T12:40:50+08:00
draft: false
keywords: ["axis", "cxf", "string[]", "WebService"]
description: "Axis调用CXF发布的WebService遇到的问题"
tags: ["axis", "cxf"]
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

客户使用**Axis**调用我方使用**CXF**发布的**WebService**报错。报错信息为意外的元素，如下图所示

![意外的参数类型](http://ocd8m6zlz.bkt.clouddn.com/意外的参数类型.png)

从报错信息看，WebService端需要的参考类型是`<{}arg0>`，而接收到的参数是`(uri:"http://tempuri.org/", local:"xmlParameter")`。经过搜索，发现需要在参数上添加注解，显式注明namespace和参数名：`@WebParam(name = "xmlParameter", targetNamespace = "http://tempuri.org/")`。修改完成后，本地测试依然报错，如下图所示

![操作不匹配](http://ocd8m6zlz.bkt.clouddn.com/操作不匹配.png)

有了之前的经验，很快发现需要在方法上添加注解注明`action`：`@WebMethod(action = "http://tempuri.org/getAllPurchasePlane")`，同时，也针对返回值也添加了注解，注明返回值的`namespace`：`@WebResult(targetNamespace = "http://tempuri.org/")`。本地使用**Axis**测试调用成功，故更新至测试系统与客户联调，发现还是报错，但报错信息变为类型转换错误，如下图所示。

![类型转换报错](http://ocd8m6zlz.bkt.clouddn.com/%E7%B1%BB%E5%9E%8B%E8%BD%AC%E6%8D%A2%E6%8A%A5%E9%94%99.png)

根据错误信息查找多次后仍未解决，此时，只好询问客户是否可以提供调用代码。获取调用代码后，发现确实调用代码有所不同，如下图所示。

![调用代码](http://ocd8m6zlz.bkt.clouddn.com/调用代码.png)

使用客户提供的调用代码测试确实调用不成功，但**WebService**确实是按照客户提供的文档发布的，同时发现**WebService**端返回值类型为`String[]`，但调用方接收到的是`List`类型，所以导致了转换出错。由此怀疑是不是客户调用方法有问题，但沟通后反馈说其他系统对接过，没有问题，此时问题陷入了死胡同。

面对这种问题，需要看透问题的本质，**WebService**的调用依靠的是**WSDL**描述调用属性，既然我们调用不成功，会不会是生成的**WSDL**有问题呢？按照这个思路，咨询客户获得了一份之前对接成功的系统的**WSDL**。经过比对，果然发现返回值类型并不是文档中写明的`String[]`，而是`ArrayOfString`，经过搜索，很快就找到了`ArrayOfString`的源码：

``` java
import java.util.ArrayList;
import java.util.List;

import javax.xml.bind.annotation.XmlAccessType;
import javax.xml.bind.annotation.XmlAccessorType;
import javax.xml.bind.annotation.XmlElement;
import javax.xml.bind.annotation.XmlType;

/**
 * <p>
 * Java class for ArrayOfString complex type.
 * <p>
 * The following schema fragment specifies the expected content contained within
 * this class.
 *
 * <pre>
 * &lt;complexType name="ArrayOfString">
 *   &lt;complexContent>
 *     &lt;restriction base="{http://www.w3.org/2001/XMLSchema}anyType">
 *       &lt;sequence>
 *         &lt;element name="string" type="{http://www.w3.org/2001/XMLSchema}string" maxOccurs="unbounded" minOccurs="0"/>
 *       &lt;/sequence>
 *     &lt;/restriction>
 *   &lt;/complexContent>
 * &lt;/complexType>
 * </pre>
 */
@XmlAccessorType(XmlAccessType.FIELD)
@XmlType(name = "ArrayOfString", propOrder = { "string" })
public class ArrayOfString {
    @XmlElement(nillable = true)
    protected List<String> string;

    /**
     * Gets the value of the string property.
     * <p>
     * This accessor method returns a reference to the live list, not a snapshot.
     * Therefore any modification you make to the returned list will be present
     * inside the JAXB object. This is why there is not a <CODE>set</CODE> method
     * for the string property.
     * <p>
     * For example, to add a new item, do as follows:
     *
     * <pre>
     * getString().add(newItem);
     * </pre>
     * <p>
     * Objects of the following type(s) are allowed in the list {@link String }
     */
    public List<String> getString() {
        if (string == null) {
            string = new ArrayList<String>();
        }
        return this.string;
    }
}
```

最终WebService代码修改如下：

``` java
@WebMethod(action = "http://tempuri.org/getAllPurchasePlane")
public @WebResult(targetNamespace = "http://tempuri.org/") ArrayOfString getAllPurchasePlane(
        @WebParam(name = "xmlParameter", targetNamespace = "http://tempuri.org/") String xmlParameter) {
    ArrayOfString ret = new ArrayOfString();
    ret.getString().add("-1");
    ret.getString().add("xml格式不正确");
    return ret;
}
```

修改代码后，本地测试通过，再和客户联调也验证通过，问题至此解决。

但疑问还没有消除，根据`ArrayofString`的源码，其实也只是封装了一个`List<String>`，为什么使用**Axis**调用后的返回值可以转换成`String[]`？希望了解的人告知一二。
