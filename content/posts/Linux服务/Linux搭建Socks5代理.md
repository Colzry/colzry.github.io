---
title: "Linux搭建Socks5代理"
description: "Linux搭理Socks"
keywords: "BT"

date: 2023-06-16T16:41:30+08:00
lastmod: 2023-06-16T16:41:30+08:00

categories:
  - Linux
tags:
  - Linux
  - 服务搭建
  - 代理
---

>  说明：
> Socks5属于明文代理，可用于正常的跳板使用；
> 比如SSH转发加速国外VPS的连接速度，特别是一些延迟高或者丢包高的VPS；
> 使用Socks5转发后SSH就可以快速稳定的连接了，解决高丢包SSH断开的问题

项目地址：[https://github.com/Lozy/danted](https://github.com/Lozy/danted)
## 1. 安装
```bash
wget https://raw.githubusercontent.com/Lozy/danted/blob/master/install.sh
# bash install.sh  --port=端口 --user=用户名 --passwd=密码
bash install.sh  --port=5566 --user=colzry --passwd=colzry_admin
```
下载不了的话可以直接写入下面的内容
```shell
#!/bin/bash
#
#   Dante Socks5 Server AutoInstall
#   -- Owner:       https://www.inet.no/dante
#   -- Provider:    https://sockd.info
#   -- Author:      Lozy
#

# Check if user is root
if [ $(id -u) != "0" ]; then
    echo "Error: You must be root to run this script, please use root to install"
    exit 1
fi

REQUEST_SERVER="https://raw.github.com/Lozy/danted/master"
SCRIPT_SERVER="https://public.sockd.info"
SYSTEM_RECOGNIZE=""

[ "$1" == "--no-github" ] && REQUEST_SERVER=${SCRIPT_SERVER}

if [ -s "/etc/os-release" ];then
    os_name=$(sed -n 's/PRETTY_NAME="\(.*\)"/\1/p' /etc/os-release)

    if [ -n "$(echo ${os_name} | grep -Ei 'Debian|Ubuntu' )" ];then
        printf "Current OS: %s\n" "${os_name}"
        SYSTEM_RECOGNIZE="debian"

    elif [ -n "$(echo ${os_name} | grep -Ei 'CentOS')" ];then
        printf "Current OS: %s\n" "${os_name}"
        SYSTEM_RECOGNIZE="centos"
    else
        printf "Current OS: %s is not support.\n" "${os_name}"
    fi
elif [ -s "/etc/issue" ];then
    if [ -n "$(grep -Ei 'CentOS' /etc/issue)" ];then
        printf "Current OS: %s\n" "$(grep -Ei 'CentOS' /etc/issue)"
        SYSTEM_RECOGNIZE="centos"
    else
        printf "+++++++++++++++++++++++\n"
        cat /etc/issue
        printf "+++++++++++++++++++++++\n"
        printf "[Error] Current OS: is not available to support.\n"
    fi
else
    printf "[Error] (/etc/os-release) OR (/etc/issue) not exist!\n"
    printf "[Error] Current OS: is not available to support.\n"
fi

if [ -n "$SYSTEM_RECOGNIZE" ];then
    wget -qO- --no-check-certificate ${REQUEST_SERVER}/install_${SYSTEM_RECOGNIZE}.sh | \
        bash -s -- $*
else
    printf "[Error] Installing terminated"
    exit 1
fi

exit 0
```

## 2. 服务端使用
卸载
```bash
bash install.sh --uninstall
```
增加用户
```bash
/etc/init.d/sockd adduser USERNAME PASSWORD
```
| **command** | **option** | **description** |
| --- | --- | --- |
| service sockd start | /etc/init.d/sockd start | start socks5 server daemon |
| service sockd stop | /etc/init.d/sockd stop | stop socks5 server daemon |
| service sockd restart | /etc/init.d/sockd restart | restart socks5 server daemon |
| service sockd reload | /etc/init.d/sockd reload | reload socks5 server daemon |
| service sockd status |  | systemd process status |
| service sockd state | /etc/init.d/sockd state | running state |
| service sockd tail | /etc/init.d/sockd tail | sock log tail |
| service sockd adduser | /etc/init.d/sockd adduser | add pam-auth user: service sockd adduser NAME PASSWORD |
| service sockd deluser | /etc/init.d/sockd deluser | delete pam-auth user: service sockd deluser NAME |

### 3. 客户端使用
proxychanins中使用（推荐）
```bash
vim /etc/proxychains.conf

socks5 43.142.174.216 5566 colzry colzry_admin
             |->ip    |->端口 |->用户名 |->密码
```
![image.png](https://cdn.nlark.com/yuque/0/2022/png/22223333/1664115803841-138510dd-8c76-4acf-b895-147a48b3500a.png#clientId=ude4ba6ec-5c43-4&crop=0&crop=0&crop=1&crop=1&from=paste&height=91&id=u21d939df&margin=%5Bobject%20Object%5D&name=image.png&originHeight=137&originWidth=817&originalType=binary&ratio=1&rotation=0&showTitle=false&size=9934&status=done&style=none&taskId=u285fd783-7ab6-4864-aee7-4e873a2f6a4&title=&width=544.6666666666666)

使用系统导出代理（不推荐）
```bash
export ALL_PROXY=socks5://colzry:colzry_admin@43.142.174.216:5566

# 取消代理
unset ALL_PROXY
```
