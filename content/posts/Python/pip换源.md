---
title: "pip换源"
description: "pip换源"
keywords: "pip"

date: 2023-06-16T16:41:30+08:00
lastmod: 2023-06-16T16:41:30+08:00

categories:
  - Python
tags:
  - Python
  - pip
---

## 安装pip

[安装脚本链接](https://bootstrap.pypa.io/get-pip.py)

```shell
python get-pip.py install
```

## Linux

Linux下，修改 ~/.pip/pip.conf (没有就创建一个文件夹及文件。文件夹要加“.”，表示是隐藏文件夹)
内容如下：

```shell
[global]
index-url = https://pypi.tuna.tsinghua.edu.cn/simple
[install]
trusted-host = https://pypi.tuna.tsinghua.edu.cn
```

## Windows

windows下，直接在user目录中创建一个pip目录，如：C:\Users\xx\pip，然后新建文件pip.ini，即 %HOMEPATH%\pip\pip.ini，在pip.ini文件中输入以下内容（以豆瓣镜像为例）：

```shell
[global]
index-url = http://pypi.douban.com/simple
[install]
trusted-host = pypi.douban.com
```
