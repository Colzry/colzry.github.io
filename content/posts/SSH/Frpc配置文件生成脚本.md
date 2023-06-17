---
title: "Frpc配置文件生成脚本"
description: "Frpc配置文件生成脚本"
keywords: "frp"

date: 2023-06-16T16:41:30+08:00
lastmod: 2023-06-16T16:41:30+08:00

categories:
  - SSH
tags:
  - FRP
---

```bash
#!/bin/bash

#================================================================
#   Copyright (C) 2022 IEucd Inc. All rights reserved.
#   
#   FileName：rand.sh
#   Author：Colzry, colzry@163.com
#   CreateDate：2022-10-28
#   Description：
#
#================================================================

read -p "请输入要穿透的端口: " target_port

function mimvp_app_port() {
    min=$1
    max=$2
    mid=$(($max-$min+1))
    num=$(head -n 20 /dev/urandom | cksum | cut -f1 -d ' ')
    randnum=$(($num%$mid+$min))

    # 排除的端口号 20000,30000,40000,50000, 可以任意添加
    port_exclude='10001,20000,30000,40000,50000'
    flag=`echo ${port_exclude} | grep ${randnum} | wc -l`
    while [ "$flag" -eq "1" ]
    do
        num=$(head -n 20 /dev/urandom | cksum | cut -f1 -d ' ')
        randnum=$(($num%$mid+$min))
        flag=`echo ${port_exclude} | grep ${randnum} | wc -l`
    done
    echo $randnum
}
# 生成随机端口
rand_port=$(mimvp_app_port 10001 50000)
# 生成随机隧道名
rand_name=`echo $RANDOM | md5sum | cut -c11-20`

cat << EOF
[common]
server_addr = frp.104300.xyz
server_port = 7000
tcp_mux = true
protocol = tcp
token = www.126126.xyz
dns_server = 223.5.5.5

[$rand_name]
type = tcp
local_ip = 127.0.0.1
local_port = $target_port
remote_port = $rand_port
EOF
```