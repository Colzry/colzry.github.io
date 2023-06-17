---
title: "Linux隧道脚本"
description: "Linux隧道脚本"
keywords: "隧道"

date: 2023-06-16T16:41:30+08:00
lastmod: 2023-06-16T16:41:30+08:00

categories:
  - SSH
tags:
  - 隧道
---

```bash
#!/bin/bash

remote_ip=43.142.174.216
remote_user="colzry"

# 默认转发的目标ip
tran_addr=127.0.0.1

# 默认访问的远程端口号
remote_port=5408

if [ $# -lt 1 ]
then
    echo -e '后面至少要有转发的端口号\n'
    exit
fi

# 转发的目标端口
tarn_port=$1

echo "要转发的端口为 $tarn_port "

echo -e "\n ===========开始转发================ \n"
echo "访问地址 $remote_ip:$remote_port"
ssh -N -g -R $remote_port:$tran_addr:$tarn_port $remote_user@$remote_ip
```