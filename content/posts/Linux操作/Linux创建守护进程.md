---
title: "Linux创建守护进程"
description: "Linux创建守护进程"
keywords: "进程"

date: 2023-06-16T16:41:30+08:00
lastmod: 2023-06-16T16:41:30+08:00

categories:
  - Linux
tags:
  - Linux
---

在 **/etc/systemd/system/** 下新建并编辑 xxx.service 文件

这里以 **code-server.service** 为例

```shell
sudo vim /etc/systemd/system/code-server.service
```

写入下面的内容并根据自己创建的服务进行更改(**_带注释的为选填，看服务的需求开启_**)

```shell
[Unit] 
Description=code-server #描述要启动的进程
After=network.target

[Service]
Type=simple
#WorkingDirectory=/root/workDir #启动进程的文件夹
#User=root #你想用什么用户启动该进程
#Group=root #你希望用什么用户组启动该进程
Restart=on-failure #进程错误时重启
RestartSec=10
ExecStart=/usr/bin/code-server#启动命，要用绝对路径，否则会报错

[Install]
WantedBy=multi-user.target
```

```bash
systemctl daemon-reload
```
