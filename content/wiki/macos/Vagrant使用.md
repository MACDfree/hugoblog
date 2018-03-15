---
title: "Vagrant使用"
date: 2017-10-15T22:29:41+08:00
draft: false
categories: ["MacOS"]
tags: ["vagrant"]
---

虚拟机管理软件Vagrant使用记录
<!--more-->

添加镜像

``` sh
vagrant box add
```

删除镜像

``` sh
vagrant box remove
```

列出镜像

``` sh
vagrant box list
```

初始化虚拟机（需要新建文件夹）

``` sh
vagrant init
```

启动虚拟机

``` sh
vagrant up
```

关闭虚拟机

``` sh
vagrant halt
```

重启虚拟机

``` sh
vagrant reload
vagrant reload --provision #可以执行Vagrantfile中的脚本
```

导出镜像

``` sh
vagrant package --output xxx.box
```

Vagrantfile文件配置

端口转发

```
config.vm.network "forwarded_port", guest: 80, host: 8080
```

私有地址

```
config.vm.network "private_network", ip: "192.168.33.10"
```

公有地址

```
config.vm.network "public_network", ip: "192.168.1.222"
```

虚拟机运行参数

```
config.vm.provider "virtualbox" do |vb|
    vb.name = "centos_test"
    vb.memory = "1024"
    vb.cpus = 2
end
```

设置主机名

```
config.vm.hostname = "macdpc"
```
