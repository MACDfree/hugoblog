---
title: "DIRECTORY管理"
date: 2017-10-16T20:09:54+08:00
draft: false
categories: ["Oracle"]
tags: []
---

使用数据泵导入备份时需要根据DIRECTORY来获取备份文件所在路径

<!--more-->

#### 创建文件夹

``` sql
CREATE [OR REPLACE] DIRECTORY <directory> AS '<pathname>';
```

directory为文件夹的别名

pathname为具体的路径

#### 设置权限

``` sql
GRANT READ[,WRITE] ON DIRECTORY <directory> TO <username>;
```

directory为文件夹的别名

username为用户名

#### 查询文件夹

``` sql
select * from dba_directories;
```

#### 删除文件夹

``` sql
drop directory <directory>;
```

directory为文件夹的别名
