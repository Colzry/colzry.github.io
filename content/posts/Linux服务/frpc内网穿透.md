---
title: "FRP内网穿透"
description: "FRP内网穿透"
keywords: "frp"

date: 2023-06-16T16:41:30+08:00
lastmod: 2023-06-16T16:41:30+08:00

categories:
  - Linux
tags:
  - Linux
  - 服务搭建
  - FRP
---

## 1. 下载Frp
```bash
wget https://gitpy.colzry.tk/github.com/fatedier/frp/releases/download/v0.48.0/frp_0.48.0_linux_amd64.tar.gz

wget https://github.com/fatedier/frp/releases/download/v0.48.0/frp_0.48.0_linux_amd64.tar.gz
```

## 2. 服务端安装
### 2.1 解压文件
```bash
tar -zxvf frp_0.48.0_linux_amd64.tar.gz
cd frp_0.48.0_linux_amd64/
cp frps /usr/local/bin/
```

### 2.2 编写配置文件
```bash
mkdir /etc/frps

vim /etc/frps/frps.ini
```

```bash
[common]
bind_port = 7000
token = czyadmin
```

### 2.3 启动
```bash
sudo vim /lib/systemd/system/frps.service
```

```bash
[Unit]
Description=Frp Server Service
After=network.target

[Service]
Type=simple
User=nobody
Restart=on-failure
RestartSec=5s
ExecStart=/usr/local/bin/frps -c /etc/frps/frps.ini
ExecReload=/usr/local/bin/frps reload -c /etc/frps/frps.ini
LimitNOFILE=1048576

[Install]
WantedBy=multi-user.target
```

```bash
sudo systemctl daemon-reload
sudo systemctl start frps.service
sudo systemctl enable --now frps.service
```

## 3. 客户端安装


### 3.1 解压文件
```bash
tar -zxvf frp_0.44.0_linux_amd64.tar.gz
cd frp_0.44.0_linux_amd64/
cp frpc /usr/local/bin/
```

> frpc.ini 配置文件详解

```bash
[common]
#远程frp服务器地址，可ip可域名
server_addr = frp02.wefinger.club
#远程frp服务器通信端口
server_port = 7000
#特权密钥
token = 12345678

#http穿透
[demo-http]
#穿透类型
type = http
#本地监听ip
local_ip = 127.0.0.1
#本地监听端口，欲穿透转发端口。
local_port = 8080
#自定义穿透域名，该域名需要解析至frp服务器。
custom_domains = testhttp.frp02.wefinger.club

#https穿透
[demo-https]
type = https
local_ip = 127.0.0.1
local_port = 8088
custom_domains = testhttps.frp02.wefinger.club

#tcp穿透，例如ssh、ftp服务
[demo-tcp]
type = tcp
 #欲穿透地址，本地必须可访问。
local_ip = 127.0.0.1
#可批量绑定，使用`,`分隔，或者使用`-`定义端口段。
local_port = 22
#绑定远程端口，可批量绑定，使用`,`分隔，或者使用`-`定义端口段。
remote_port = 20022

#udp穿透,例如转发dns服务
[demo-udp]
type = udp
local_ip = 8.8.8.8
local_port = 53
remote_port = 20053
```

### 3.2 启动

> 配置文件根据各网站的粘贴过来就行

#### 3.2.1 直接启动

**启动**
```bash
./frpc -c ./frpc.ini &
```
#### 3.2.2 后台启动

**配置后台自动启动**
```bash
sudo vim /lib/systemd/system/frpc.service
```

```bash
[Unit]
Description=Frp Client Service
After=network.target

[Service]
Type=simple
User=nobody
Restart=on-failure
RestartSec=5s
ExecStart=/usr/local/bin/frpc -c /etc/frpc/frpc.ini
ExecReload=/usr/local/bin/frpc reload -c /etc/frpc/frpc.ini
LimitNOFILE=1048576

[Install]
WantedBy=multi-user.target
```

```bash
sudo systemctl daemon-reload
sudo systemctl start frpc.service
sudo systemctl enable frpc.service
```

### 3.3 配置多个隧道
创建配置文件目录
```bash
mkdir -p /etc/frpc
```
打开配置文件的目录，编写对应的配置文件
```bash
cd /etc/frpc
vim 102SSH.ini
```
配置示例如下
```bash
[common]
server_addr = cn-gydx-bgp-1.openfrp.top
server_port = 8120
tls_enable = true
tcp_mux = true
protocol = tcp
user = 8022c3fe7ba2d9d8c953f899b86da17a
tls_enable = true
token = mnE3A8hIWapwShje
dns_server = 114.114.114.114
 
[102UH]
privilege_mode = true
type = tcp
local_ip = 192.168.5.102
local_port = 1022
remote_port = 60856
use_encryption = false
use_compression = false
```
配置System服务
```bash
vim /lib/systemd/system/frpc@.service
```
```bash
[Unit]
Description=Frp Client Service
After=network.target

[Service]
Type=simple
User=nobody
Restart=on-failure
RestartSec=5s
ExecStart=/usr/local/bin/frpc -c /etc/frpc/%i.ini
ExecReload=/usr/local/bin/frpc reload -c /etc/frpc/%i.ini
LimitNOFILE=1048576

[Install]
WantedBy=multi-user.target
```
**根据配置文件名称来启动服务**
```bash
systemctl daemon-reload
systemctl start frpc@102SSH
systemctl status frpc@102SSH
systemctl enable frpc@102SSH
```
如果您忘记了之前开启过哪些隧道，使用下面的命令可以列出当前运行中的隧道
```bash
systemctl list-units frpc@*
```

如果您忘记了之前设置过的自启隧道，可以使用下面的命令列出
```bash
systemctl list-units --all frpc@*
```
