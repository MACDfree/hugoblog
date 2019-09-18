---
title: "Hadoop+Hive环境搭建"
date: 2017-10-12T22:10:56+08:00
categories: ["BigData"]
tags: ["hadoop", "hive"]
---

`Hadoop`+`Hive`环境搭建过程说明及注意事项
<!--more-->

# 安装包版本说明

安装包  | 版本
---    |---
Hadoop | 2.8.1
Hive   | 2.3.0

# 安装Hadoop

## 创建用户hadoop

``` sh
# 创建hadoop用户
Shell> useradd -m hadoop -s /bin/bash
# 设置密码
Shell> passwd hadoop
```

## 修改hostname

``` sh
# 修改hostname
Shell> vim /etc/hosts
```

![hosts](/images/clipboard.png)

修改后重启，使用hadoop用户登录

## 设置免登录

``` sh
# 生成密钥对
Shell> ssh-keygen -t rsa
# 追加到authorized_keys中
Shell> cat ./id_rsa.pub >> ./authorized_keys
# 设置权限
Shell> chmod 644 authorized_keys
# 将下载好的hadoop和hive安装包解压缩到opt目录下
Shell> tar -zxvf filename
```

## 添加环境变量

``` sh
Shell> vim .bashrc
```

追加如下内容

``` sh
export JAVA_HOME=/usr/lib/jvm/java
export HADOOP_HOME=/opt/hadoop
export HIVE_HOME=/opt/hive2
export PATH=$PATH:$JAVA_HOME/bin:$HADOOP_HOME/bin:$HIVE_HOME/bin
```

使修改生效

``` sh
Shell> source .bashrc
```

## 修改配置文件

修改$HADOOP_HOME/etc/hadoop/core-site.xml

``` xml
<configuration>
    <property>
        <name>hadoop.tmp.dir</name>
        <value>file:/opt/hadoop/tmp</value>
    </property>
    <property>
        <name>fs.defaultFS</name>
        <value>hdfs://hadoop001:9000</value>
    </property>
    <property>
        <name>hadoop.proxyuser.hadoop.hosts</name>
        <value>*</value>
    </property>
    <property>
        <name>hadoop.proxyuser.hadoop.groups</name>
        <value>*</value>
    </property>
</configuration>
```

注意`hadoop.tmp.dir`配置，防止系统重启后被操作系统删除，配置`fs.defaultFS`为指定HDFS的namenode

修改$HADOOP_HOME/etc/hadoop/hdfs-site.xml

``` xml
<configuration>
    <property>
        <name>dfs.replication</name>
        <value>1</value>
    </property>
    <property>
        <name>dfs.namenode.name.dir</name>
        <value>file:/home/hadoop/data/dfs/name</value>
    </property>
    <property>
        <name>dfs.datanode.data.dir</name>
        <value>file:/home/hadoop/data/dfs/data</value>
    </property>
</configuration>
```

配置项`dfs.replication`为HDFS的副本数，由于使用伪分布式，所以设置为1；配置项`dfs.namenode.name.dir`和`dfs.datanode.data.dir`分别为设置namenode和datanode的存储路径

修改$HADOOP_HOME/etc/hadoop/mapred-site.xml

``` xml
<configuration>
    <property>
        <name>mapreduce.framework.name</name>
        <value>yarn</value>
    </property>
    <property>
        <name>mapreduce.jobhistory.address</name>
        <value>hadoop001:10020</value>
    </property>
    <property>
        <name>mapreduce.jobhistory.webapp.address</name>
        <value>hadoop001:19888</value>
    </property>
</configuration>
```

修改$HADOOP_HOME/etc/hadoop/yarn-site.xml

``` xml
<configuration>
    <property>
        <name>yarn.nodemanager.aux-services</name>
        <value>mapreduce_shuffle</value>
    </property>
    <property>
        <name>yarn.nodemanager.aux-services.mapreduce.shuffle.class</name>
        <value>org.apache.hadoop.mapred.ShuffleHandler</value>
    </property>
    <property>
        <name>yarn.resourcemanager.address</name>
        <value>hadoop001:8032</value>
    </property>
    <property>
        <name>yarn.resourcemanager.scheduler.address</name>
        <value>hadoop001:8030</value>
    </property>
    <property>
        <name>yarn.resourcemanager.resource-tracker.address</name>
        <value>hadoop001:8035</value>
    </property>
    <property>
        <name>yarn.resourcemanager.admin.address</name>
        <value>hadoop001:8033</value>
    </property>
    <property>
        <name>yarn.resourcemanager.webapp.address</name>
        <value>hadoop001:8088</value>
    </property>
</configuration>
```

格式化namenode

``` sh
Shell> hdfs namenode –format
```

启动服务

``` sh
#（在$HADOOP/sbin下）
Shell> ./start-dfs.sh
Shell> ./start-yarn.sh
# 或者
Shell> ./start-all.sh
```

WebUI管理界面

`http://192.168.131.21:50070`     HDFS管理界面

`http://192.168.131.21:8088`       MR管理界面

# 安装Hive

解压Hive到opt目录下

添加环境变量HIVE_HOME和PATH

添加Hive相关目录

``` sh
Shell> hadoop fs -mkdir       /tmp
Shell> hadoop fs -mkdir       /user/hive/warehouse
Shell> hadoop fs -chmod g+w   /tmp
Shell> hadoop fs -chmod g+w   /user/hive/warehouse
```

复制一个hive-site.xml（$HIVE_HOME/conf）

``` sh
Shell> cp hive-default.xml.template hive-site.xml
```

修改hive.site.xml文件

1. 使用MySQL存储元数据

    MySQL驱动：javax.jdo.option.ConnectionDriverName
    com.mysql.jdbc.Driver
    数据库连接字符串：javax.jdo.option.ConnectionURL
    jdbc:mysql://192.168.131.1:3306/metastore_db?createDatabaseIfNotExist=true
    数据库连接用户名：javax.jdo.option.ConnectionUserName
    数据库连接密码：javax.jdo.option.ConnectionPassword
    需要将MySQL的驱动添加到hive的lib下

2. 设置hive用户名密码

    用户名：hive.server2.keystore.path
    密码：hive.server2.keystore.password
    最好设置为hadoop，否则会有权限问题

3. 替换变量

    主要是${system:java.io.tmpdir}和${system:user.name}两个，需要修改为真实的绝对路径，例如：/opt/hive2/root/

初始化数据库

``` sh
Shell> schematool -dbType mysql -initSchema
```

启动服务

``` sh
Shell> hiveserver2
```

连接hive数据库

``` sh
Shell> beeline -u jdbc:hive2://192.168.131.21:10000 -n hadoop -p hadoop
```