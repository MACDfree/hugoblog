---
title: "Git常用命令"
date: 2017-10-12T22:57:49+08:00
lastmod: 2018-03-27T21:41:51+08:00
categories: ["Git"]
tags: []
---

`Git`常用命令记录
<!--more-->

# 初始化Git仓库

``` bash
git init
```

# 跟踪所有改动文件

``` bash
git add .
git add -i # 交互式添加跟踪文件
```

# 停止跟踪文件但不删除

``` bash
git rm --cached <file>
```

# 提交所有更新过的文件

``` bash
git commit -m 'commit message'
```

# 删除未跟踪的所有文件

``` bash
git clean -f # 删除未跟踪的文件
git clean -fd # 删除未跟踪的文件和文件夹
# 使用git clean前先使用-n参数预览需要删除的文件，防止误删，如 git clean -nf
```

# 撤销工作目录中所有未提交的修改

``` bash
git reset --hard HEAD
```

# 全局设置

``` bash
git config --global user.name "macdfree"
git config --global user.email "macdfree@163.com"
```

# 切换Tag

``` bash
git checkout <tagname>
```

# 创建并切换分支

``` bash
git checkout -b <branchname>
```

相当于

``` bash
git branch <branchname>
git checkout <branchname>
```

# 推送分支至远程仓库

``` bash
git pubash origin <branchname>
```

# 合并远程仓库中的更改

使用场景为：在github中用户B`fork`了用户A的项目，经过一段时间后，用户A又修改了项目，此时用户B想同步用户A的修改内容。

操作步骤：

1. 添加用户A的项目地址

    ``` bash
    git remote add upstream <URL>
    ```

1. fetch用户A的项目

    ``` bash
    git fetch upstream
    ```

1. 将远程仓库合并到本地仓库

    ``` bash
    git merge upstream/master
    ```

    合并操作中可能会出现冲突无法合并，此时需要手动解决冲突后提交

1. 推送本地仓库至用户B的远程仓库