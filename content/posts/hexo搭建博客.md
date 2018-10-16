---
title: hexo搭建博客
tags:
  - hexo
date: 2016-05-29T03:48:32
categories: ["hexo"]
---

在网上看见使用Hexo和GitHub Page搭建博客的文章，闲来无事就仿照着做了一下。起初是在我的Windows电脑搭建，但觉得Windows和命令行不怎么协调，就改用我的树莓派搭建。

<!--more-->

参考[https://hexo.io/docs](https://hexo.io/docs)

# 1. 安装Node.js、Hexo和Git

安装Node.js，Hexo官网推荐的使用nvm

cURL：

``` bash
curl https://raw.github.com/creationix/nvm/master/install.sh | sh
```

Wget：

``` bash
wget -qO- https://raw.github.com/creationix/nvm/master/install.sh | sh
```

在树莓派上cURL方式测试失败，只能使用Wget方式。

安装完nvm后执行：

``` bash
nvm install 4
```

参数4表示安装Node.js的版本。

安装Hexo：

``` bash
npm install -g hexo-cli
```

Git安装：

``` bash
sudo apt-get install git
```

# 2. 初始化及配置

切换到需要创建博客的目录，执行以下指令

``` bash
hexo init
```

修改_config.yml配置文件中的一些基本信息，参考[Hexo配置](https://hexo.io/docs/configuration.html)

需要再安装一些常用模块，在博客所在目录下执行以下指令：

``` bash
npm install hexo-generator-feed --save # 用于生成feed
npm install hexo-generator-sitemap --save # 用于生成站点地图
```

同时_config.yml中添加相应配置

```yaml
# feed
feed:
  type: atom
  path: atom.xml
  limit: 20
  hub:

# sitemap
sitemap:
  path: sitemap.xml
```

# 3. 使用Hexo新建文章并生成发布

新建文章

``` bash
hexo new [layout] <title>
```

其中layout为模板，可以在scaffolds目录中进行修改

生成静态页面

``` bash
hexo generate
```

发布文章
由于本人使用Git发布方式，所以需要先安装git发布模块并在_config.yml中添加配置

``` bash
npm install hexo-deployer-git --save #安装git发布模块
```

```yaml
deploy:
  type: git
  repo: <repository url>
  branch: [branch]
  message: [message]
```

完成上述操作后执行以下指令及发布成功

``` bash
hexo deploy
```

另外还可以使用 `hexo server` 命令进行本地浏览，此命令需要安装模块

``` bash
npm install hexo-server --save
```

由于树莓派性能有限，`hexo server` 命令本地浏览会很缓慢，故不经常使用。
