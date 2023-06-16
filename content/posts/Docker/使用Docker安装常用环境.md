---
title: "使用Docker安装常用环境"
date: 2023-06-16T10:10:26+08:00
categories:
 - Docker
tags:
  - Docker
---

## 安装Docker
```shell
$ curl -fsSL get.docker.com -o get-docker.sh
$ sudo sh get-docker.sh --mirror Aliyun
```
卸载
```bash
dnf remove -y -q docker-ce docker-ce-cli containerd.io docker-scan-plugin docker-compose-plugin docker-ce-rootless-extras
rm -rf 
```
### 启动 Docker
```shell
$ sudo systemctl enable docker
$ sudo systemctl start docker
```

### 建立 docker 用户组
默认情况下，`docker` 命令会使用 [Unix socket](https://en.wikipedia.org/wiki/Unix_domain_socket) 与 Docker 引擎通讯。而只有 `root` 用户和 `docker` 组的用户才可以访问 Docker 引擎的 Unix socket。出于安全考虑，一般 Linux 系统上不会直接使用 `root` 用户。因此，更好地做法是将需要使用 `docker` 的用户加入 `docker` 用户组。

建立 `docker` 组：
```shell
$ sudo groupadd docker
```

将当前用户加入 `docker` 组：
```shell
$ sudo usermod -aG docker $USER

注销用户或者重启系统
```
### 更换镜像源
```bash
vim /etc/docker/daemon.json

{"registry-mirrors": ["http://hub-mirror.c.163.com"]}

systemctl restart docker

2) 腾讯云 docker hub mirror
https://mirror.ccs.tencentyun.com

3) 华为云
https://05f073ad3c0010ea0f4bc00b7105ec20.mirror.swr.myhuaweicloud.com

4) docker中国
https://registry.docker-cn.com

5) 网易
http://hub-mirror.c.163.com

6) daocloud
http://f1361db2.m.daocloud.io
```
## 安装Docker图形化界面
```shell
#下载 Docker 图形化界面 portainer
sudo docker pull portainer/portainer
#创建 portainer 容器
sudo docker volume create portainer_data
#运行 portainer
sudo docker run -d \
  -p 9000:9000 \
  --name portainer \
  --restart always \
  -v /var/run/docker.sock:/var/run/docker.sock \
  -v portainer_data:/data \
  portainer/portainer
```
## 安装MySql
```shell
//拉取MySQL镜像
docker pull mysql
//启动MySQL，注意更改密码，用户名为root，密码czyadmin
docker run -d \
  --name mysql \
  --restart=always \
  -p 3306:3306 \
  -e MYSQL_ROOT_PASSWORD=czyadmin \
  mysql
```

## 安装Redis
```shell
//拉取Redis镜像
docker pull redis
//启动Redis，注意更改密码，用户名为root，密码czyadmin
docker run -d \
  --name redis \
  --restart=always \
  -p 6379:6379 \
  redis \
  --requirepass "czyadmin"
```

## 安装Nginx
拉取镜像
```shell
docker pull nginx
```
创建本地配置文件
```shell
mkdir -p /etc/nginx/conf.d && vim /etc/nginx/nginx.conf
```
```nginx
user  nginx;
worker_processes  auto;

error_log  /var/log/nginx/error.log notice;
pid        /var/run/nginx.pid;


events {
    worker_connections  1024;
}


http {
    include       /etc/nginx/mime.types;
    default_type  application/octet-stream;

    log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
                      '$status $body_bytes_sent "$http_referer" '
                      '"$http_user_agent" "$http_x_forwarded_for"';

    access_log  /var/log/nginx/access.log  main;

    sendfile        on;
    #tcp_nopush     on;

    keepalive_timeout  65;

    #gzip  on;

    include /etc/nginx/conf.d/*.conf;
}
```
启动服务
```shell
//启动nginx，映射本地配置文件
docker run -d \
  --name nginx \
  --restart=always \
  -p 80:80 \
  -v /etc/nginx/nginx.conf:/etc/nginx/nginx.conf \
  -v /etc/nginx/conf.d:/etc/nginx/conf.d \
  nginx
```
```bash
vim /etc/nginx/conf.d/demo.conf

server {
    listen       80;
    listen  [::]:80;
    server_name  localhost;

    location / {
        root   /usr/share/nginx/html;
        index  index.html index.htm;
    }
}


docker exec nginx bash -c 'nginx -s reload'
```
## 安装RabbitMQ
```bash
docker pull rabbitmq:management 
```
> **默认用户名和密码：guest/guest**

```bash
docker run -dit \
--name rabbitmq \
--restart=always \
-p 5672:5672 \
-p 15672:15672 \
rabbitmq:management
```
| 端口 | 作用 |
| --- | --- |
| 15672 | 管理界面UI的使用端口 |
| 15671 | 管理监听端口 |
| 5672,5671 | AMQP 0-9-1 without and with TLSclient端通信口 |
| 4369 | (epmd)epmd代表Erlang端口映射守护进程，erlang发现口 |
| 25672 | ( Erlang distribution) server间内部通信口 |

## 安装Postgresql
```bash
docker pull postgres
```
```bash
docker run -d \
  --name postgres \
  --restart=always \
  -p 5432:5432 \
  -e POSTGRES_PASSWORD=czyadmin \
  postgres
```
> 用户名：postgres	密码：czyadmin

