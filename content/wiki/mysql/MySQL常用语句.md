---
title: "MySQL常用语句"
date: 2017-10-15T22:51:08+08:00
draft: false
categories: ["MySQL"]
tags: []
---

删除数据库

``` sql
drop database if exists epointbid_bigdata_pro;
```
<!--more-->

删除视图

``` sql
drop view if exists view_frame_user_java;
```

查看慢查询语句

``` sql
show full processlist;
kill 慢查询的id;
```

添加字段

``` sql
ALTER TABLE tablename ADD column type COMMENT 'message';
```

添加索引

``` sql
ALTER TABLE tablename ADD INDEX indexname(`column`);
```

查看MySQL全局变量

``` sql
show global variables like '%timeout%';
```

设置自增列的值

``` sql
alter table users AUTO_INCREMENT=10000;
```

查看数据库

``` sql
show databases;
```
