---
title: "Linux配置环境变量的建议"
description: "Linux配置环境变量的建议"
keywords: "环境变量"

date: 2023-06-16T16:41:30+08:00
lastmod: 2023-06-16T16:41:30+08:00

categories:
  - Linux
tags:
  - Linux
---

> 若是普通用户，则在自己用户目录的`.bashrc`中配置，若使用了`zsh`，则在`.zshrc`中配置
>  
> 若是root用户，则在`/etc/profile.d/`中新建一个`my_env.sh`文件
>  
> 最后普通用户(`source`)刷新`.bashrc`或`.zshrc`文件，root用户刷新`/etc/profile`文件


其中`$PAHT`表示系统的环境变量，**:** 表示拼接在系统环境变量`$PAHT`之后

### eg:

#### root用户下的my_env.sh文件

```shell
#JAVA_HOME
export JAVA_HOME=/opt/module/jdk1.8.0_212
export PATH=$PATH:$JAVA_HOME/bin

#HADOOP_HOME
export HADOOP_HOME=/opt/module/hadoop-3.1.3
export PATH=$PATH:$HADOOP_HOME/bin
export PATH=$PATH:$HADOOP_HOME/sbin
```

```shell
source /etc/profile
```

#### 普通用户的.zshrc文件

```shell
export PATH=/home/colzry/.local/bin/:$PATH
```

```shell
 source .zshrc
```

#### 最后 /etc/environment也可以更改，改完之后注销即可

或者执行下面语句

```shell
PATH="$PATH"
```
