---
title: "Linux搭建WebDav服务"
description: "Linux搭建WebDav服务"
keywords: "WebDav"

date: 2023-06-16T16:41:30+08:00
lastmod: 2023-06-16T16:41:30+08:00

categories:
  - Linux
tags:
  - Linux
  - 服务搭建
  - WebDav
---

## WebDav Server
WebDAV 是 GitHub 上开源的项目，基于 Go 语言实现，不仅跨平台，还支持 ARM 架构，可在㠌入式设备中部署 WebDAV 服务器。
项目地址：[https://github.com/hacdias/webdav](https://github.com/hacdias/webdav)
GitHub 下载对应的架构 WebDAV，解压后获得 webdav二进制文件
## 1. 解压
```bash
tar -zxvf linux-amd64-webdav.tar.gz
mv webdav /usr/bin/
```
![image.png](https://cdn.nlark.com/yuque/0/2022/png/22223333/1657879613565-e2a8f725-e486-4ceb-8225-54b8b5db9bbc.png#clientId=ub16f6b8f-9c2e-4&crop=0&crop=0&crop=1&crop=1&from=paste&height=126&id=uce74f936&margin=%5Bobject%20Object%5D&name=image.png&originHeight=126&originWidth=561&originalType=binary&ratio=1&rotation=0&showTitle=false&size=27768&status=done&style=none&taskId=ud7edb94e-7d33-4ebd-8798-8f09760a3a1&title=&width=561)
## 2. 编写配置文件
```bash
vim /opt/webdav_config.yaml
```
```bash
# Server related settings
address: 0.0.0.0
port: 10105
# 如果无需验证填 false
auth: true
# 如果不需要 https 则填 false
tls: false
# https证书和密钥，如果 tls 为 false，cert 和 key 不需要
# cert: /data/www/cert/szhome.xf1024.com_nginx/cert.pem
# key: /data/www/cert/szhome.xf1024.com_nginx/cert.key
# 访问前缀，建议默认
prefix: /
debug: false

# 如果 auth 为 false 生效，文件共享的路径
scope: .
modify: true
rules: []

# 跨域设置
cors:
  enabled: true
  credentials: true
  allowed_headers:
    - Depth
  allowed_hosts:
    - http://localhost:10105
  allowed_methods:
    - GET
  exposed_headers:
    - Content-Length
    - Content-Range

# 用户信息，如果 auth 为 true 生效
users:
  - username: Colzry
    password: webdav_colzry
    scope: /mnt/hhd01/back/Video/
    # 是否允许修改
    modify: true
  - username: other_user
    password: xxxxx
    scope: /data/1/Video
    modify: true
```
使用命令
```bash
/usr/bin/webdav -c /opt/webdav_config.yaml
```
## 3. 添加守护进程
```bash
vim /usr/lib/systemd/system/webdav.service
```
```bash
[Unit]
Description=WebDAV server
After=network.target

[Service]
Type=simple
User=root
ExecStart=/usr/bin/webdav -c /opt/webdav_config.yaml
Restart=on-failure

[Install]
WantedBy=multi-user.target
```
```bash
systemctl daemon-reload
systemctl start webdav.service
systemctl status webdav.service
systemctl enable webdav.service
```
## 4. Linux 挂载
```bash
sudo apt install davfs2
sudo mount -t davfs http://192.168.5.254:10105/ /webdav
```
## 5. Nginx 开启 WebDAV
在Nginx中实现WebDAV需要安装 libnginx-mod-http-dav-ext 模块，以下是Nginx的配置：
```bash
server {
        listen 80;
        listen [::]:80;
 
        server_name dav.engr-z.com;

        location / {
                root /data/webdav;
                client_body_temp_path /var/temp;
                dav_methods PUT DELETE MKCOL COPY MOVE;
                dav_ext_methods PROPFIND OPTIONS;
                create_full_put_path on;
                client_max_body_size 10G;
        }
}
 
server {
        listen 443;
        listen [::]:443;
        server_name dav.engr-z.com;
 
        ssl on;
        ssl_certificate /data/www/cert/dav.engr-z.com_nginx/cert.pem;
        ssl_certificate_key /data/www/cert/dav.engr-z.com_nginx/cert.key;
        ssl_session_timeout 5m;
        ssl_protocols SSLv2 SSLv3 TLSv1;
        ssl_ciphers ALL:!ADH:!EXPORT56:RC4+RSA:+HIGH:+MEDIUM:+LOW:+SSLv2:+EXP;
        ssl_prefer_server_ciphers on;
 
        location / {
                root /data/webdav;
                client_body_temp_path /var/temp;
                dav_methods PUT DELETE MKCOL COPY MOVE;
                dav_ext_methods PROPFIND OPTIONS;
                create_full_put_path on;
                client_max_body_size 10G;
        }
 
}
```
