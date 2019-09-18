---
title: SublimeText插件编写备忘
date: 2016-10-06T21:43:07
categories:
- note
tags: 
- Sublime Text
---

最近本来想玩一下Atom，奈何插件真不会写（JS功底太差，CoffeeScript不想用），导致最后又重投了Sublime Text的怀抱。Sublime Text的插件两年前就已经写过，只是当时也就网上各种搜，东拼西凑出来个，也没有好好记录一下，所以这次来补上。

<!--more-->

# 1. 如何新建一个插件

`Tools->Developer->New Pluging` 可以新建一个默认的插件，代码如下：

``` python
import sublime
import sublime_plugin


class ExampleCommand(sublime_plugin.TextCommand):
    def run(self, edit):
        self.view.insert(edit, 0, "Hello, World!")
```

插件文件保存路径类似为 `Sublime Text Build 3103 x64\Data\Packages`。

# 2. 指令的命名规则

新建并保存好插件后可以使用 Ctrl+\` 呼出交互窗口，输入 `view.run_command('example')` 即可插入 `Hello, World!` 这边要注意指令的命名规则转换，即将驼峰式命名规则转成下划线分割。如：类名是 `AaaBbbCommand` 则在调用run_command时需要转成 `aaa_bbb`。

# 3. TextCommand、WindowCommand和ApplicationCommand

默认生成的代码中继承了`TextCommand`，但其实还有`WindowCommand`和`ApplicationCommand`这两个基类。对于这三者的关系，我的理解是：`TextCommand`对应的是`sublime.View`，`WindowCommand`对应的是`sublime.Window`，`ApplicationCommand`对应的是`Sublime Module`；然后是一个Sublime程序会有多个Window，一个Window又会有多个View。

![示意图](/images/awv.jpg)

# 4. run_command常用的内置指令

`TextCommand`、`WindowCommand`和`ApplicationCommand`对于的类都有相应的run_command方法，Sublime Text本身也内置了一些基本的指令，如set_layout、close_window和new_window等。其中set_layout调用时的参数有些复杂，如果要生成一个三列的布局，代码如下：

``` python
new_window.run_command(
           'set_layout',
           {
               "cols": [0.0, 0.33, 0.66, 1.0], # 3列等分
               "rows": [0.0, 1.0], # 一行
               "cells": [[0, 0, 1, 1], [1, 0, 2, 1], [2, 0, 3, 1]] # 这个我也不知道 -_-|||
           })
```
