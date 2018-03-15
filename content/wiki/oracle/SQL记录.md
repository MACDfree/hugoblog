---
title: "SQL记录"
date: 2017-10-16T20:06:00+08:00
draft: false
categories: ["Oracle"]
tags: []
---

#### 查看用户表空间

``` sql
select distinct(tablespace_name) from dba_segments where owner=upper('EpointBid_TP7');
```

<!--more-->

#### 查看Oracle版本

``` sql
select * from v$version;
```

#### 查看表大小

由于user_tables表中数据不是实时的，所有需要先执行

``` sql
select 'analyze table '||S.TABLE_NAME||' compute statistics;' from  user_tables s;
```

再执行

``` sql
select t.table_name,t.num_rows from user_tables t ORDER BY NUM_ROWS DESC;
```

#### 添加字段

``` sql
alter table tablename add (column datatype [default value][null/not null],….);
```

#### 修改字段

``` sql
alter table tablename modify (column datatype [default value][null/not null],….);
```

#### 删除字段

``` sql
alter table tablename drop (column);
```