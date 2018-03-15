---
title: Oracle导库
date: 2016-09-29T00:10:57
categories: ["Oracle"]
tags: ["导库", "数据泵"]
---

Oracle导库主要分成清库、清空回收站、导库三个步骤。

<!--more-->
1. 清库

    将`Function`、`Procedures`、`Packages`、`Package bodies`、`Tables`、`Triggers`、`views`、`Sequences`都删除

2. 清空回收站

    执行`purge recyclebin`，把drop后回收站中的数据清空

3. 导库
    1. imp导入

        `imp 用户名/密码@ip:port/数据库名 file=d:\sh\shjc_1211.dmp log=d:\sh\imp_shjc_1211.log full=y ignore=y`，其中`用户名/密码@ip:port/数据库名`为数据库连接，格式为：`用户名/密码@192.168.208.120:1521/orcl`，`file`为备份文件的路径，`log`为日志文件路径。

    2. impdp导入

        `impdp 用户名/密码@ip:port/数据库名 directory=dir_dp dumpfile=dept.dmp remap_schema=scott:system remap_tablespace=users:tbs1`，`directory`参数指定要导入的备份所在的文件夹，可以用`select * from dba_directories;`查看文件夹；`dumpfile`参数指定备份文件名；`remap_schema`为重新指定schema，格式为`src:des`；`remap_tablespace`为重新指定tablespace，格式为`src:des`。

注：使用数据泵方式导入时，遇到版本不兼容的问题，原因是使用高版本的oracle导出的备份，在低版本上导入。解决方法是在导出语句后加上`version`参数指定导入数据库的版本

---

补充

2016年9月29日

今天测试服务器上有一张表搞坏了，然后老大让我从备份中单独还原这张表的数据，这边也记录一下

`imp 用户名/密码@ip:port/数据库名 file=E:\EpointBid_TP6j20160929.dmp log=‪E:\EpointBid_TP6j20160929.log tables=(table_struct)`

其实就是加了一个`tables`参数来指定需要导入的表。另外，今天也发现就算数据库备份和需要导入的数据库表空间不一样也是可以导入成功的。
