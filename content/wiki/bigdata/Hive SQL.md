---
title: "Hive SQL"
date: 2017-10-12T22:55:53+08:00
categories: ["BigData"]
tags: ["hive"]
---

`Hive SQL`相关记录
<!--more-->

建表示例

``` sql
sql> create table r_user (id_user bigint,login string,password string,name string,description string,enabled string) row format delimited fields terminated by ';' stored as textfile location '/user/hadoop/db/bigdata/r_user';
```

注意`location`指定的文件夹，默认文件夹名称和表名称一致
