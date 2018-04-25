---
title: "SQL记录"
date: 2017-10-16T20:06:00+08:00
lastmod: 2018-04-24T10:45:56+08:00
draft: false
categories: ["Oracle"]
tags: []
---

记录常用Oracle SQL语句
<!--more-->

# 查看用户表空间

``` sql
select distinct(tablespace_name) from dba_segments where owner=upper('EpointBid_TP7');
```

# 查看Oracle版本

``` sql
select * from v$version;
```

# 查看表大小

由于user_tables表中数据不是实时的，所以需要先执行

``` sql
select 'analyze table '||S.TABLE_NAME||' compute statistics;' from  user_tables s;
```

再执行

``` sql
select t.table_name,t.num_rows from user_tables t ORDER BY NUM_ROWS DESC;
```

# 添加字段

``` sql
alter table tablename add (column datatype [default value][null/not null],….);
```

# 修改字段

``` sql
alter table tablename modify (column datatype [default value][null/not null],….);
```

# 删除字段

``` sql
alter table tablename drop (column);
```

# 统计用户表字段数

``` sql
select *
from (select
        count(1) cc,
        a.table_name
      from user_col_comments a left join user_tab_comments b on a.table_name = b.table_name
      where b.table_type = 'TABLE'
      group by a.table_name) t
order by t.cc desc;
```