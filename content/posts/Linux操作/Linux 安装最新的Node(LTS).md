---
title: "Linux安装最新的Node(LTS)"
description: "Linux安装最新的Node(LTS)"
keywords: "node"

date: 2023-06-16T16:41:30+08:00
lastmod: 2023-06-16T16:41:30+08:00

categories:
  - Linux
tags:
  - Linux
  - Node
---

```shell
# 先更新软件源
$ sudo apt-get upadte
# 安装npm 
$ sudo apt install npm
# 使用npm全局安装n模块
$ sudo npm install n -g
# 安装最新长期支持版node
$ sudo n lts
# 检查是否安装成功
$ node -v
```
