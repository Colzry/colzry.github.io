---
title: "安装Redis"
description: "安装Redis"
keywords: "Redis"

date: 2023-06-16T16:41:30+08:00
lastmod: 2023-06-16T16:41:30+08:00

categories:
  - Redis
tags:
  - Redis
---

## 1. 编译安装
```bash
wget https://codeload.github.com/redis/redis/tar.gz/refs/tags/7.0.4 -O redis-7.0.4.tar.gz

tar zxf redis-7.0.4.tar.gz

apt install build-essential

cd /redis-7.0.4/src

make install

cd ../

sed -i 's|daemonize no|daemonize yes|' redis.conf
sed -i 's|# supervised auto|supervised no|' redis.conf
sed -i 's|bind 127.0.0.1 ::1|bind 0.0.0.0|' redis.conf
sed -i 's|protected-mode yes|protected-mode no|' redis.conf
sed -i 's|# requirepass foobared|requirepass czyadmin|' redis.conf
mkdir /etc/redis
cp redis.conf /etc/redis/
/usr/local/bin/redis-server /etc/redis/redis.conf
```

```bash
vim /lib/systemd/system/redis.service
```
```bash
[Unit]
Description=Redis data structure server
Documentation=https://redis.io/documentation
Wants=network-online.target
After=network-online.target

[Service]
Type=forking
ExecStart=/usr/local/bin/redis-server /etc/redis/redis.conf
ExecStop=/bin/kill -s TERM $MAINPID
PIDFile=/run/redis/redis-server.pid
TimeoutStopSec=0
Restart=always
RuntimeDirectory=redis
RuntimeDirectoryMode=2755

UMask=007
PrivateTmp=yes
LimitNOFILE=65535
PrivateDevices=yes
ProtectHome=yes

[Install]
WantedBy=multi-user.target
```
```bash
systemctl daemon-reload
systemctl start redis.service
```
## 2. 命令行安装
```bash
apt install redis-server -y
```
