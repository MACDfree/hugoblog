---
title: "Git常用命令"
date: 2017-10-12T22:57:49+08:00
categories: ["Git"]
tags: []
---

`Git`常用命令记录
<!--more-->

#### 初始化Git仓库

``` sh
git init
```

#### 跟踪所有改动文件

``` sh
git add .
git add -i # 交互式添加跟踪文件
```

#### 停止跟踪文件但不删除

``` sh
git rm --cached <file>
```

#### 提交所有更新过的文件

``` sh
git commit -m 'commit message'
```

#### 删除未跟踪的所有文件

``` sh
git clean -f # 删除未跟踪的文件
git clean -fd # 删除未跟踪的文件和文件夹
# 使用git clean前先使用-n参数预览需要删除的文件，防止误删，如 git clean -nf
```

#### 撤销工作目录中所有未提交的修改

``` sh
git reset --hard HEAD
```

#### 全局设置

``` sh
git config --global user.name "macdfree"
git config --global user.email "macdfree@163.com"
```

#### 切换Tag

``` sh
git checkout <tagname>
```

#### 创建并切换分支
``` sh
git checkout -b <branchname>
```
相当于
``` sh
git branch <branchname>
git checkout <branchname>
```

#### 推送分支至远程仓库
``` sh
git push origin <branchname>
```
