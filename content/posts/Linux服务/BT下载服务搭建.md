---
title: "BT下载服务搭建"
description: "BT下载服务搭建"
keywords: "BT"

date: 2023-06-16T16:41:30+08:00
lastmod: 2023-06-16T16:41:30+08:00

categories:
  - Linux
tags:
  - Linux
  - 服务搭建
---

github项目地址： [https://github.com/P3TERX/aria2.sh](https://github.com/P3TERX/aria2.sh)
## 0. 新加入Docker安装
文档地址：[https://p3terx.com/archives/docker-aria2-pro.html](https://p3terx.com/archives/docker-aria2-pro.html)
### 0.1 安装aria2
自行替换`RPC_SECRET`后启动
```bash
docker run -d \
    --name aria2-pro \
    --restart always \
    --log-opt max-size=1m \
    --network host \
    -e PUID=$UID \
    -e PGID=$GID \
    -e RPC_SECRET=czyadmin \
    -e RPC_PORT=6800 \
    -e LISTEN_PORT=6888 \
    -v $PWD/aria2-config:/config \
    -v $PWD/aria2-downloads:/downloads \
    p3terx/aria2-pro
```
### 0.2 安装WEBUI
```bash
docker run -d \
    --name ariang \
    --restart always \
    --log-opt max-size=1m \
    -p 6880:6880 \
    p3terx/ariang
```
### 0.3 安装filebrowser
默认用户名和密码：admin
```bash
docker run -d \
    --name filebrowser \
    --restart always \
    -v $PWD/aria2-downloads:/srv \
    -v $PWD/filebrowser/filebrowser.db:/database/filebrowser.db \
    -v $PWD/filebrowser/settings.json:/config/settings.json \
    -e PUID=$(id -u) \
    -e PGID=$(id -g) \
    -p 8080:80 \
    filebrowser/filebrowser
```

## 普通安装
### 1.0 安装并配置aria2
```bash
wget https://picture-czy.oss-cn-beijing.aliyuncs.com/shareFile/aria2.sh -O /root/aria2.sh && bash /root/aria2.sh
```

### 2.0 设置tracker自动更新

```bash
wget https://picture-czy.oss-cn-beijing.aliyuncs.com/shareFile/tracker.sh -O /root/tracker.sh && bash /root/tracker.sh

crontab -e

# 每周日凌晨5点
0 5 * * 0 /bin/bash /root/tracker.sh 2>&1
```

### 3.0 安装AriaNg
```bash
wget https://picture-czy.oss-cn-beijing.aliyuncs.com/shareFile/AriaNg-1.2.3-AllInOne.zip -O /root/AriaNg.zip

apt install nginx -y

unzip /root/AriaNg.zip -d /var/www/AriaNg

vim /etc/nginx/sites-enabled/AriaNg

server {
    listen 80;  #监听端口
    server_name 127.0.0.1;    #主机ip
    index index.html index.htm;
    location / {
        root /var/www/AriaNg; #站点目录
    }
}


# 检查语法错误
nginx -t 

# 重启服务
systemctl restart nginx.service
```
**填入RPC密钥**
![image (1).png](https://cdn.nlark.com/yuque/0/2022/png/22223333/1657896587289-f344abc4-b593-45f4-a991-21118ab2a716.png#clientId=u5aceb2ae-04d3-4&crop=0&crop=0&crop=1&crop=1&errorMessage=unknown%20error&from=paste&height=413&id=ua9b9396f&margin=%5Bobject%20Object%5D&name=image%20%281%29.png&originHeight=413&originWidth=1920&originalType=binary&ratio=1&rotation=0&showTitle=false&size=60455&status=error&style=none&taskId=uae032089-894b-401e-91e0-30271ba54d7&title=&width=1920)
### 4.0 安装filebrowser

```bash
wget https://picture-czy.oss-cn-beijing.aliyuncs.com/shareFile/linux-amd64-filebrowser.tar.gz

tar -zxvf linux-amd64-filebrowser.tar.gz -C /usr/local/bin/

#先创建一个目录用来存放数据库和配置文件
mkdir /etc/filebrowser/


source ~/.bashrc
#############   国外新方法   ##################

curl -fsSL https://raw.githubusercontent.com/filebrowser/get/master/get.sh | bash

#创建配置数据库
filebrowser -d /etc/filebrowser/filebrowser.db config init
#设置监听地址
filebrowser -d /etc/filebrowser/filebrowser.db config set --address 0.0.0.0
#设置监听端口
filebrowser -d /etc/filebrowser/filebrowser.db config set --port 8080
#设置语言环境
filebrowser -d /etc/filebrowser/filebrowser.db config set --locale zh-cn
#设置日志位置
filebrowser -d /etc/filebrowser/filebrowser.db config set --log /var/log/filebrowser.log
#添加一个用户
filebrowser -d /etc/filebrowser/filebrowser.db users add admin password --perm.admin
#设置网盘根目录
filebrowser -d /etc/filebrowser/filebrowser.db config set --root /mnt/hhd01/aria2

# 启动
filebrowser -d /etc/filebrowser/filebrowser.db
```

- Username: `admin`
- Password: `password`
- 后台挂起

```bash
nohup filebrowser -d /etc/filebrowser/filebrowser.db &
```

或者设置守护进程

```bash
vim /lib/systemd/system/filebrowser.service


[Unit] 
Description=File Browser
After=network.target

[Service]
Type=simple
ExecStart=/usr/local/bin/filebrowser -d /etc/filebrowser/filebrowser.db
Restart=on-abnormal
RestartSec=5s
KillMode=mixed

[Install]
WantedBy=multi-user.target



systemctl daemon-reload

systemctl start filebrowser.service

systemctl status filebrowser.service

systemctl enable filebrowser.service
```

