---
title: "SpeedTest测速"
description: "SpeedTest测速"
keywords: "SpeedTest"

date: 2023-06-16T16:41:30+08:00
lastmod: 2023-06-16T16:41:30+08:00

categories:
  - Linux
tags:
  - Linux
  - 服务搭建
  - SpeedTest
---

## 1. 安装脚本
```bash
curl -fsSL git.io/speedtest-cli.sh | sudo bash
```
下载不了可以新建
```bash
#!/usr/bin/env bash
#
# Copyright (c) 2020-2021 P3TERX <https://p3terx.com>
#
# This is free software, licensed under the MIT License.
# See /LICENSE for more information.
#
# https://github.com/P3TERX/script
# File name: speedtest-cli.sh
# Description: Install Ookla Speedtest CLI
# System Required: GNU/Linux
# Version: 1.3
#

set -o errexit
set -o errtrace
set -o pipefail

Green_font_prefix="\033[32m"
Red_font_prefix="\033[31m"
Green_background_prefix="\033[42;37m"
Red_background_prefix="\033[41;37m"
Font_color_suffix="\033[0m"
INFO="[${Green_font_prefix}INFO${Font_color_suffix}]"
ERROR="[${Red_font_prefix}ERROR${Font_color_suffix}]"

PROJECT_NAME='Ookla Speedtest CLI'
BIN_DIR='/usr/local/bin'
BIN_NAME='speedtest'
BIN_FILE="${BIN_DIR}/${BIN_NAME}"

if [[ $(uname -s) != Linux ]]; then
    echo -e "${ERROR} This operating system is not supported."
    exit 1
fi

if [[ $(id -u) != 0 ]]; then
    echo -e "${ERROR} This script must be run as root."
    exit 1
fi

echo -e "${INFO} Get CPU architecture ..."
if [[ $(command -v apk) ]]; then
    PKGT='(apk)'
    OS_ARCH=$(apk --print-arch)
elif [[ $(command -v dpkg) ]]; then
    PKGT='(dpkg)'
    OS_ARCH=$(dpkg --print-architecture | awk -F- '{ print $NF }')
else
    OS_ARCH=$(uname -m)
fi
case ${OS_ARCH} in
*86)
    FILE_KEYWORD='i386'
    ;;
x86_64 | amd64)
    FILE_KEYWORD='x86_64'
    ;;
aarch64 | arm64)
    FILE_KEYWORD='aarch64'
    ;;
arm*)
    FILE_KEYWORD='arm'
    ;;
*)
    echo -e "${ERROR} Unsupported architecture: ${OS_ARCH} ${PKGT}"
    exit 1
    ;;
esac
echo -e "${INFO} Architecture: ${OS_ARCH} ${PKGT}"

echo -e "${INFO} Get ${PROJECT_NAME} download URL ..."
DOWNLOAD_URL="https://install.speedtest.net/app/cli/ookla-speedtest-1.0.0-${FILE_KEYWORD}-linux.tgz"
echo -e "${INFO} Download URL: ${DOWNLOAD_URL}"

echo -e "${INFO} Installing ${PROJECT_NAME} ..."
curl -LS "${DOWNLOAD_URL}" | tar xzC ${BIN_DIR} ${BIN_NAME}
chmod +x ${BIN_FILE}
if [[ ! $(echo ${PATH} | grep ${BIN_DIR}) ]]; then
    ln -sf ${BIN_FILE} /usr/bin/${BIN_NAME}
fi
if [[ -s ${BIN_FILE} && $(${BIN_NAME} --version) ]]; then
    echo -e "${INFO} Done."
else
    echo -e "${ERROR} ${PROJECT_NAME} installation failed !"
    exit 1
fi
```
安装完成后执行
```bash
speedtest
```
![image.png](https://cdn.nlark.com/yuque/0/2022/png/22223333/1664173378266-e51cef74-4a78-4261-9ae6-a17da5f98fa5.png#clientId=u2d0a339a-c339-4&crop=0&crop=0&crop=1&crop=1&from=paste&height=477&id=u217b7676&margin=%5Bobject%20Object%5D&name=image.png&originHeight=716&originWidth=969&originalType=binary&ratio=1&rotation=0&showTitle=false&size=69077&status=done&style=none&taskId=u556a034a-c5ff-48f8-867e-51e6475e365&title=&width=646)
