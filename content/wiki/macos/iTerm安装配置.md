---
title: "iTerm安装配置"
date: 2017-10-15T22:35:41+08:00
draft: false
categories: ["MacOS"]
tags: ["iterm"]
---

iTerm相关配置记录备忘
<!--more-->

1. 安装iTerm

2. 安装oh-my-zsh

    ``` sh
    curl -L https://raw.github.com/robbyrussell/oh-my-zsh/master/tools/install.sh | sh
    ```

3. 安装powerline

    ``` sh
    git clone https://github.com/powerline/fonts.git
    cd fonts
    ./install.sh
    ```

4. 修改iTerm配置字体

    ![字体配置](https://blog-1254016481.cos.ap-shanghai.myqcloud.com/字体配置.png)

5. 安装配置配色方案

    ``` sh
    git clone git://github.com/altercation/solarized.git
    ```

    导入配置文件

    ![导入配置文件](https://blog-1254016481.cos.ap-shanghai.myqcloud.com/导入配置文件.png)

    import配置文件

6. 使用agnoster主题

    先检查一下`~/.oh-my-zsh/themes`目录下应该有此主题文件
    编辑`~/.zshrc`文件，将`ZSH_THEME`修改为`agnoster`

    ![主题配置](https://blog-1254016481.cos.ap-shanghai.myqcloud.com/主题配置.png)


7. 设置指令高亮效果

    ``` sh
    git clone git://github.com/zsh-users/zsh-syntax-highlighting.git
    ```

    编辑~/.zshrc文件，在最后添加以下内容

    ``` sh
    source ~/zsh-syntax-highlighting/zsh-syntax-highlighting.zsh
    ```

8. 安装历史操作记录自动补全插件

    ``` sh
    git clone git://github.com/zsh-users/zsh-autosuggestions ~/.zsh/zsh-autosuggestions
    ```

    在.zshrc中添加如下语句

    ``` sh
    source ~/.zsh/zsh-autosuggestions/zsh-autosuggestions.zsh
    ```
