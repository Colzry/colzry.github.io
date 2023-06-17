---
title: "安装ZHS"
description: "安装ZHS"
keywords: "ZHS"

date: 2023-06-16T16:41:30+08:00
lastmod: 2023-06-16T16:41:30+08:00

categories:
  - Vim
tags:
  - Vim
  - ZHS
---

```shell
# 使用方法
$ git clone https://gitee.com/colzry/my_oh-my-zsh.git
$ cd my_oh-my-zsh
$ chmod +x install.sh
# 注意：如果没有安装zsh，则先安装zsh $ sudo apt install zsh
$ ./install.sh
$ mv .zshrc ../
```

```shell
# 安装插件
$ git clone https://github.com/zsh-users/zsh-autosuggestions $ZSH_CUSTOM/plugins/zsh-autosuggestions
$ git clone https://github.com/zsh-users/zsh-syntax-highlighting.git ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-syntax-highlighting
```

刷新配置文件

```shell
$ source ~/.zshrc
```
