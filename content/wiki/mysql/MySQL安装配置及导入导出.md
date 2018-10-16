---
title: "MySQL安装配置及导入导出"
date: 2017-10-15T22:55:29+08:00
draft: false
categories: ["MySQL"]
tags: []
---

mysql安装记录

<!--more-->

# Windows首次安装MySQL时初始化

``` sh
mysqld --initialize [with random root password]
mysqld --initialize-insecure [without random root password]
```

# linux安装mysql后密码重置

1. 修改/etc/my.cnf文件，添加skip-grant-tables=1配置，重启mysqld服务

2. mysql -uroot连接数据库

3. 修改密码

    ``` sql
    use mysql;
    update user set authentication_string=password('root'), password_expired='N', password_last_changed=now() where user='root';
    ```

4. 修改/etc/my.cnf文件，删除skip-grant-tables=1配置，重启mysqld服务

5. mysql -uroot -p连接数据库

6. 允许远程访问

    ``` sql
    use mysql;
    update user set host='%' where user='root';
    ```

# 补充

mysql8.0.11安装包中没有my.ini默认配置文件，需要手动新增，并添加配置

```ini
basedir=D:/software/mysql-8.0.11-winx64
datadir=D:/software/mysql-8.0.11-winx64/data
default-storage-engine=INNODB
# 默认启用ssl，本地部署可以关掉
skip_ssl
```

mysql5.7为root用户随机生成了密码，一般存放在`/var/log/mysqld.log`中

``` sh
grep "password" /var/log/mysqld.log
```

![查询随机密码](https://blog-1254016481.cos.ap-shanghai.myqcloud.com/查询随机密码.png)

可以使用此密码登录

使用此密码登录后需要通过以下语句修改密码

``` sql
alter user 'root'@'localhost' identified with mysql_native_password by '11111';
--alter user 'root'@'localhost' identified by '11111';
```

查看mysql密码长度限制

``` sql
select @@validate_password_length;
```

设置mysql密码长度限制

``` sql
set global validate_password_length=4;
```

设置mysql密码强度

``` sql
set global validate_password_policy=0;
```

设置权限

``` sql
grant all privileges on *.* to root@'%' identified by '1111';
```

刷新权限

``` sql
flush privileges;
```

导出数据库

``` sh
mysqldump -uroot -p1111 -R bigdata1 > export.sql
```

-R参数表示导出存储过程和函数

mysqldump报错

    mysqldump: Got error: 1449: The user specified as a definer (''@'') does not exist when using LOCK TABLES

此报错是由于数据库中有的definer为@，排查后发现有一个视图有问题，删除后可以正常导出

``` sql
SELECT TABLE_SCHEMA,TABLE_NAME from information_schema.views WHERE `DEFINER`='@';
```

创建utf-8编码的数据库

``` sql
create database `bigdata2` default character set utf8mb4 collate utf8mb4_general_ci;
```

导入数据库

``` sh
mysql -uroot -p1111 bigdata2 < export.sql
```
