---
title: "Maven打包添加SVN版本号和构建时间"
date: 2018-08-01T12:36:34+08:00
lastmod: 2018-08-01T12:36:34+08:00
draft: false
keywords: []
description: "Maven打包添加SVN版本号和构建时间"
tags: ["maven"]
categories: ["java"]
author: ""

# You can also close(false) or open(true) something for this content.
# P.S. comment can only be closed
comment: false
toc: false
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

为了方便判断应用服务器运行环境中的jar包是否是最新的，考虑将SVN版本号和构建时间信息添加到jar包中。

经过搜索尝试，记录一下基于Maven的实现方式。

<!--more-->

# 添加SVN版本号信息

通过搜索，不难发现有两种基于Maven实现添加SVN版本号的方式。

第一种是使用`buildnumber-maven-plugin`插件，此方式也是maven官网上推荐的方式（[链接](https://maven.apache.org/plugin-developers/cookbook/add-svn-revision-to-manifest.html)），可以将SVN版本号添加到`META-INF/MANIFEST.MF`文件中。但是，经过简单试用后发现需要在pom.xml中显式地添加配置SVN地址，这一点对于懒癌患者有点无法接受。

第二种是使用`maven-svn-revision-number-plugin`插件，此方式可以自动获取当前工程的SVN地址，获取当前工作空间的版本、状态、提交时间等信息。但是，网上搜索到的配置方式是输出到单独的配置文件中（[链接](https://blog.csdn.net/hy245120020/article/details/54405488)）。为了实现输出到`META-INF/MANIFEST.MF`中，修改了部分配置，具体配置如下：

```xml
<!-- SVN版本号 -->
<plugin>
  <groupId>com.google.code.maven-svn-revision-number-plugin</groupId>
  <artifactId>maven-svn-revision-number-plugin</artifactId>
  <version>1.7</version>
  <configuration>
    <verbose>true</verbose>
    <entries>
      <entry>
        <prefix>svn</prefix>
      </entry>
    </entries>
  </configuration>
  <executions>
    <execution>
      <id>revision</id>
      <phase>validate</phase>
      <goals>
        <goal>revision</goal>
      </goals>
    </execution>
  </executions>
  <dependencies>
    <dependency>
      <groupId>org.tmatesoft.svnkit</groupId>
      <artifactId>svnkit</artifactId>
      <version>1.8.5</version>
    </dependency>
  </dependencies>
</plugin>
<plugin>
  <groupId>org.apache.maven.plugins</groupId>
  <artifactId>maven-jar-plugin</artifactId>
  <version>2.4</version>
  <configuration>
    <archive>
      <manifestEntries>
        <SVN-Version>${svn.revision}</SVN-Version>
      </manifestEntries>
    </archive>
  </configuration>
</plugin>
```

# 添加构建时间信息

Maven官网上推荐的方式是使用`maven-antrun-plugin`插件实现（[链接](https://maven.apache.org/plugin-developers/cookbook/add-build-time-to-manifest.html)），但发现配置略繁琐，想寻找其他简单的可替代方案。此时目光重新聚焦到了上节中的`buildnumber-maven-plugin`插件上，因为在此插件的介绍中提到

> This plugin works in one of 3 ways: with an SCM, with a sequential build number, or with a timestamp.

描述中的时间戳方式感觉应该满足需求，尝试后确实可以，具体配置如下：

```xml
<!-- 构建时间 -->
<plugin>
  <groupId>org.codehaus.mojo</groupId>
  <artifactId>buildnumber-maven-plugin</artifactId>
  <version>1.4</version>
  <configuration>
    <timestampFormat>yyyy-MM-dd HH:mm:ss.S</timestampFormat>
    <timestampPropertyName>buildTime</timestampPropertyName>
  </configuration>
  <executions>
    <execution>
      <id>create-timestamp</id>
      <phase>validate</phase>
      <goals>
        <goal>create-timestamp</goal>
      </goals>
    </execution>
  </executions>
</plugin>
<plugin>
  <groupId>org.apache.maven.plugins</groupId>
  <artifactId>maven-jar-plugin</artifactId>
  <version>2.4</version>
  <configuration>
    <archive>
      <manifestEntries>
        <SVN-Version>${svn.revision}</SVN-Version>
        <Build-Time>${buildTime}</Build-Time>
      </manifestEntries>
    </archive>
  </configuration>
</plugin>
```

注意不要忘记在`maven-jar-plugin`中添加构建时间的配置。

至此，在jar包中添加SVN版本号和构建时间信息的功能已全部完成，效果如下：

![maven添加svn版本信息](/images/maven添加svn版本信息.png)

**另外，上述pom.xml中的配置在eclipse中会报错，忽略即可。**
