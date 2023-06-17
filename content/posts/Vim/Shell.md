---
title: "Shell脚本常用配置"
description: "Shell脚本常用配置"
keywords: "Shell"

date: 2023-06-16T16:41:30+08:00
lastmod: 2023-06-16T16:41:30+08:00

categories:
  - Vim
tags:
  - Vim
  - Shell
---

## 1. 颜色
```bash
RED='\033[31m'
GREEN='\033[32m'
YELLOW='\033[33m'
BLUE='\033[34m'
PLAIN='\033[0m'
BOLD='\033[1m'
SUCCESS='[\033[32mOK\033[0m]'
COMPLETE='[\033[32mDONE\033[0m]'
WARN='[\033[33mWARN\033[0m]'
ERROR='[\033[31mERROR\033[0m]'
WORKING='[\033[34m*\033[0m]'
```
```bash
echo -e "\n $SUCCESS success"
echo -e "\n $COMPLETE complete"
echo -e "\n $WARN warn"
echo -e "\n $ERROR error"
echo -e "\n $WORKING working"


echo -e "\n ${GREEN} green ${PLAIN}"
echo -e "\n ${BOLD} bold"
```
输出结果
![image.png](https://cdn.nlark.com/yuque/0/2022/png/22223333/1659958911519-75123af9-3e17-43c4-9d27-f96cbb8db60b.png#clientId=ud5a51bfd-73b4-4&crop=0&crop=0&crop=1&crop=1&from=paste&height=275&id=u551c94aa&margin=%5Bobject%20Object%5D&name=image.png&originHeight=412&originWidth=414&originalType=binary&ratio=1&rotation=0&showTitle=false&size=13359&status=done&style=none&taskId=ud5f8ee81-cbf5-4c8a-bc8e-5289c1f2b63&title=&width=276)
## 2. sed
[https://www.cnblogs.com/maxincai/p/5146338.html](https://www.cnblogs.com/maxincai/p/5146338.html)
```bash
$ cat test.ini
[mysql]
basedir=
datadir=

# colzry @ ThinkBooK in ~ [23:30:08]
$ sed -ri "s@(basedir=)@\/usr\/local\/bin@g" ./test.ini

# colzry @ ThinkBooK in ~ [23:30:12]
$ cat test.ini
[mysql]
basedir=/usr/local/bin
datadir=
```

```bash
## 定义目录和文件
RedHatRelease=/etc/redhat-release
DebianVersion=/etc/debian_version

## 系统判定变量
function EnvJudgment() {
    ## 判定当前系统基于 Debian or RedHat
    if [ -s $RedHatRelease ]; then
				echo -e "\n yum 系列\n"
    elif [ -s $DebianVersion ]; then
				echo -e "\n apt 系列\n"
    else
        echo -e "\n 无法判断当前运行环境，请先确认本脚本针对当前操作系统是否适配\n"
        exit
    fi   
}

## 环境判定
function PermissionJudgment() {
    ## 权限判定
    if [ $UID -ne 0 ]; then
        echo -e "\n 权限不足，请使用 Root 用户\n"
        exit
    fi
}

```
