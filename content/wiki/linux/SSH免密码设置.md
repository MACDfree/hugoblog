---
title: "SSH免密码设置"
date: 2017-10-15T22:19:59+08:00
draft: false
categories: ["Linux"]
tags: ["ssh"]
---

实现SSH证书登录
<!--more-->

1. 在客户端生成公钥和 私钥

    ``` sh
    ssh-keygen -t rsa
    ```

    生成位置为~/.ssh下，默认为id_rsa和id_rsa.pub文件

2. 将公钥上传至服务器（如果服务器端没有.ssh文件夹，则需要手动新建）

    ``` sh
    scp .ssh/id_rsa.pub username@hostip:.ssh/id_rsa.pub
    ```

3. 在服务器端新建authorized_keys文件（如果已存在则不需要新建）（此文件在.ssh文件夹中）

    ``` sh
    touch authorized_keys
    chmod 600 authorized_keys
    ```

4. 将公钥内容追加到authorized_keys文件中

    ``` sh
    cat id_rsa.pub >> authorized_keys
    ```

补充：

确保.ssh目录权限为700，authorized_keys文件权限为600