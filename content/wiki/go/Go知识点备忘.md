---
title: "Go知识点备忘"
date: 2017-10-12T23:02:15+08:00
lastmod: 2018-03-20T10:52:57+08:00
categories: ["Go"]
tags: []
---

`Go`相关知识点记录
<!--more-->

# =和:=的区别

=用于赋值，:=声明变量并赋值且进行自动类型推断
=必须先使用var进行声明

``` go
var a
a=100
// 或
var b=100
// 或
var c int=100
// :=声明并赋值，不能加var，也不能加类型
d:=100
```

# slice类型和array类型变量初始化差异

数组：[n]T

切片：[]T

``` go
s := []int{0,1,2,3,4,5} // slice
s := [...]int{0,1,2,3,4,5} // array
values := [9]int{} // array
```

切片初始化时不需要指定长度，数组初始化时需要指定长度

# 类型转换

``` go
T(v)
```

# 文件覆写

``` go
file, err := os.OpenFile(common.ConfigPath, os.O_WRONLY|os.O_TRUNC, 0600)
defer file.Close()
_, err = io.WriteString(file, str)
```

其中关键是`os.O_TRUNC`，查看注释表示：如果可能，在打开文件时清空文件。