---
title: "Gogs服务搭建"
description: "Gogs服务搭建"
keywords: "Gogs"

date: 2023-06-16T16:41:30+08:00
lastmod: 2023-06-16T16:41:30+08:00

categories:
  - Linux
tags:
  - Linux
  - 服务搭建
  - Gogs
---

# Gogs服务搭建

## 1. 安装
```bash
wget https://github.com/gogs/gogs/releases/download/v0.13.0/gogs_0.13.0_linux_amd64.tar.gz
# 网络不好可以使用下面的
wget https://picture-czy.oss-cn-beijing.aliyuncs.com/shareFile/gogs_0.13.0_linux_amd64.tar.gz

tar -zxvf gogs_0.13.0_linux_amd64.tar.gz -C /usr/local

# 修改目录拥有者，如无用户先添加 U:G
chown -R gogs:gogs gogs/
```

> 无用户的先添加用户，并为用户赋予gogs目录的权限

```bash
#创建用户组
groupadd gogs
#创建用户家目录
mkdir -p /home/gogs
#创建用户 
useradd -g gogs -d /home/gogs -s /bin/bash gogs
#用户家目录赋权755 4->r 2->w 1->x
chmod -R 755 /home/gogs
#修改gogs目录拥有者
chown -R gogs:gogs /usr/local/gogs/
```

## 2. 添加守护进程
在安装目录的`scripts/systemd`下有官方的脚本可以参考
以下的内容经过修改，若启动不成功可以尝试更改`custom/conf/`目录下的配置文件

```bash
vim /lib/systemd/system/gogs.service

[Unit]
Description=Gogs
After=syslog.target
After=network.target

[Service]
Type=simple
User=gogs
Group=gogs
WorkingDirectory=/usr/local/gogs
ExecStart=/usr/local/gogs/gogs web
Restart=always
Environment=USER=gogs HOME=/home/gogs

[Install]
WantedBy=multi-user.target


systemctl daemon-reload
systemctl start gogs.service
systemctl status gogs.service
systemctl enable gogs.service --now
```