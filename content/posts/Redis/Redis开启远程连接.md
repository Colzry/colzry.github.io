---
title: "Redis开启远程连接"
description: "Redis开启远程连接"
keywords: "Redis"

date: 2023-06-16T16:41:30+08:00
lastmod: 2023-06-16T16:41:30+08:00

categories:
  - Redis
tags:
  - Redis
---

1. 让防火墙打开6379端口
2. 修改redis配置文件(/etc/redis/redis.conf)
```bash
# 允许任何主机连接、访问
bind 127.0.0.1 改为 bind 0.0.0.0
 
# 关闭保护模式
protected-mode yes 改为 protected-mode no
 
# 允许启动后在后台运行，即关闭命令行窗口后仍能运行
daemonize no 改为 daemonize yes
```

3. 重启redis并指定配置文件
```bash
sudo /etc/init.d/redis-server restart

redis-server /etc/redis/redis.conf
```
