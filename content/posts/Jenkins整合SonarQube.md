---
title: "Jenkins整合sonarQube"
date: 2018-07-28T17:14:20+08:00
lastmod: 2018-07-28T17:14:20+08:00
draft: true
keywords: []
description: "Jenkins整合SonarQube"
tags: ["sonarqube", "jenkins"]
categories: ["持续集成"]
author: ""

# You can also close(false) or open(true) something for this content.
# P.S. comment can only be closed
comment: false
toc: true
autoCollapseToc: false
postMetaInFooter: true
hiddenFromHomePage: false
# You can also define another contentCopyright. e.g. contentCopyright: "This is another copyright."
contentCopyright: ""
reward: true
mathjax: false
mathjaxEnableSingleDollar: false

flowchartDiagrams:
  enable: false
  options: ""

sequenceDiagrams: 
  enable: false
  options: ""

---

本文主要记录在CentOS7中安装SonarQube，以及将SonarQube整合到Jenkins的过程

<!--more-->

# 安装SonarQube

## 系统要求

详细要求参见：[https://docs.sonarqube.org/display/SONAR/Requirements](https://docs.sonarqube.org/display/SONAR/Requirements)

这边简要描述一下基本的要求：

1. 对于小型团队至少需要4GB内存，对于企业用户则需要8核16GB配置；
2. 通常使用的Oracle JRE，版本必须是JRE8
3. 由于SonarQube使用到了Elasticsearch，对于Linux环境需要设置：

```bash
sysctl -w vm.max_map_count=262144
sysctl -w fs.file-max=65536
ulimit -n 65536
ulimit -u 2048
```

## 下载安装包

下载路径：[https://www.sonarqube.org/downloads/](https://www.sonarqube.org/downloads/)

笔者下载的是长期支持版本6.7.4

## CentOS7环境下安装

由于Elasticsearch不能使用root用户运行，所以先需要新建用户，可参考[用户新增并授权]({{< ref "/wiki/linux/用户新增并授权" >}})。使用此用户下载安装包并解压缩，解压缩后目录结构如下图所示

![sonarqube目录结构](http://ocd8m6zlz.bkt.clouddn.com/sonarqube目录结构.png)

接下来需要创建数据库，笔者使用MySQL数据库，创建完成后需要修改配置文件

```bash
vim conf/sonar.properties
```

![sonarube数据库修改](http://ocd8m6zlz.bkt.clouddn.com/sonarube数据库修改.png)

在启动之前，需要赋予一下执行权限

```bash
chmod +x bin/linux-x86-64/sonar.sh
chmod +x bin/linux-x86-64/wrapper
chmod +x elasticsearch/bin/*
```

以上准备工作完成后就可以启动SonarQube了

```bash
bin/linux-x86-64/sonar.sh start
```

稍等片刻启动完成后，即可访问`http://ipaddress:9000`

# SonarQube配置

## 更新插件

使用admin/admin登录系统，点击配置/应用市场进行插件的更新和安装。建议可以安装中文语言包以便于使用，在搜索框中输入`chinese`，选择`Chinese Pack`进行安装。另外，对于需要用到插件也可以进行更新。注意，安装和更新插件需要重启后才生效。

![sonarqube更新插件](http://ocd8m6zlz.bkt.clouddn.com/sonarqube更新插件.png)

## 配置SVN账号密码

由于代码使用SVN进行管理，在分析代码时SonarQube需要有SVN权限，设置如下图所示，首先需要启用SCM，然后配置用户名和密码。

![sonarqube设置svn账号](http://ocd8m6zlz.bkt.clouddn.com/sonarqub设置svn账号.png)

## 生成Token

由于下面需要和Jenkins进行整合，所以此处需要生成一个Token供Jenkins使用。生成后切记将此Token保存下来，供Jenkins整合时使用。

![sonarqubeToken设置](http://ocd8m6zlz.bkt.clouddn.com/sonarqubeToken设置.png)

至此，SonarQube的配置基本完成，下面就轮到Jenkins上场了。

# Jenkins整合SonarQube

## SonarQube插件安装配置

在Jenkins中点击系统管理/管理插件，搜索`SonarQube Scanner`安装，安装完成重启后，点击系统管理/系统设置，定位到如下图所示位置

![sonarqube整合Jenkins配置](http://ocd8m6zlz.bkt.clouddn.com/sonarqube整合Jenkins配置.png)

其中`Server URL`即SonarQube的访问地址，`Server authentication token`即上一节中生成的Token。

## 项目配置

选择需要分析的项目，点击配置，添加构建后步骤，如下图所示

![sonarqube整合Jenkins项目配置](http://ocd8m6zlz.bkt.clouddn.com/sonarqube整合Jenkins项目配置.png)

![sonarqube整合Jenkins项目配置2](http://ocd8m6zlz.bkt.clouddn.com/sonarqube整合Jenkins项目配置2.png)

```ini
sonar.projectKey=projectKey
sonar.projectName=projectName
sonar.projectVersion=1.0
sonar.language=java
sonar.java.binaries=$WORKSPACE/target/classes/
sonar.sources=$WORKSPACE/src
```

配置完成后点击立即构建，构建成功后可在SonarQube中看到代码的分析结果。

![sonarube分析结果](http://ocd8m6zlz.bkt.clouddn.com/sonarube分析结果.png)
