---
title: "Redis常用命令"
date: 2017-10-16T20:19:39+08:00
draft: false
categories: ["Redis"]
tags: []
---

#### 进入redis命令行

``` sh
redis-cli
```

<!--more-->

#### 查看redis基本信息

```
info
```

#### 查看客户端连接数

```
info clients
```

#### 删除当前数据库中的所有Key

```
flushdb
```

#### 删除所有数据库中的key

```
flushall
```

#### 查看客户端列表

```
client list
```
