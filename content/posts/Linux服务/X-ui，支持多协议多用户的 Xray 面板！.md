---
title: "X-UI，支持多协议多用户的Xray面板"
description: "X-UI，支持多协议用户的Xray面板"
keywords: "X-UI"

date: 2023-06-16T16:41:30+08:00
lastmod: 2023-06-16T16:41:30+08:00

categories:
  - Linux
tags:
  - Linux
  - 服务搭建
  - XUI
---


# X-ui，支持多协议多用户的 Xray 面板！

## 1.0 安装x-ui

-  x-ui地址：[https://github.com/vaxilu/x-ui](https://github.com/vaxilu/x-ui) 
-  acme脚本地址：[https://github.com/acmesh-official/acme.sh](https://github.com/acmesh-official/acme.sh) 

#### 1.0.1更新及安装组件

```bash
apt update -y # Debian/Ubuntu 命令
apt install -y curl #Debian/Ubuntu 命令
apt install -y socat #Debian/Ubuntu 命令
```

```bash
yum update -y #CentOS 命令
yum install -y curl #CentOS 命令
yum install -y socat #CentOS 命令
# 关闭防火墙
systemctl disable firewalld.service
systemctl stop firewalld.service
```

#### 1.0.2 安装 Acme 脚本
```bash
curl https://get.acme.sh | sh
```

#### 1.0.3 80 端口空闲的证书申请方式

自行更换代码中的域名、邮箱为你解析的域名及邮箱

```bash
~/.acme.sh/acme.sh --set-default-ca --server letsencrypt  /zerossl
~/.acme.sh/acme.sh --register-account -m xxxx@xxxx.com
~/.acme.sh/acme.sh --issue -d mydomain.com --standalone
```

#### 1.0.4 安装证书到指定文件夹（选做）
自行更换代码中的域名为你解析的域名
```bash
~/.acme.sh/acme.sh --installcert -d mydomain.com --key-file /root/private.key --fullchain-file /root/cert.crt
```

### 1.0.5 安装 & 升级 X-ui 面板

**安装及升级的一键代码**

```bash
bash <(curl -Ls https://raw.githubusercontent.com/vaxilu/x-ui/master/install.sh)
```

## 2.0 节点配置及功能讲解

### 2.0.1 更改面板端口，根路径，用户名和密码

![image_JNIwOjoe4O.png](https://cdn.nlark.com/yuque/0/2022/png/22223333/1663739779381-fe3ebc43-308a-42f2-b2b5-1ecd3c63ebce.png#clientId=u9a0ac102-2889-4&crop=0&crop=0&crop=1&crop=1&from=paste&height=493&id=u222de251&name=image_JNIwOjoe4O.png&originHeight=740&originWidth=1917&originalType=binary&ratio=1&rotation=0&showTitle=false&size=79217&status=done&style=none&taskId=u36da2efd-ef8e-463b-91e0-17a011870a5&title=&width=1278)

### 2.0.2 更改xray版本到最新(选做)

![image_jZo9uGZIX9.png](https://cdn.nlark.com/yuque/0/2022/png/22223333/1663739800984-872e7a73-7846-4735-a26d-be91257d1e44.png#clientId=u9a0ac102-2889-4&crop=0&crop=0&crop=1&crop=1&from=paste&height=424&id=u2211eca6&name=image_jZo9uGZIX9.png&originHeight=636&originWidth=1920&originalType=binary&ratio=1&rotation=0&showTitle=false&size=98275&status=done&style=none&taskId=u02579274-b9a9-47cd-a3da-c583eb839fc&title=&width=1280)

### 2.0.3 创建节点

> **vmess协议**


![image_QN9Y-CXOy3.png](https://cdn.nlark.com/yuque/0/2022/png/22223333/1663739833930-7bde5de8-f999-433c-9654-8500036d7750.png#clientId=u9a0ac102-2889-4&crop=0&crop=0&crop=1&crop=1&from=paste&height=496&id=u17193d32&name=image_QN9Y-CXOy3.png&originHeight=744&originWidth=650&originalType=binary&ratio=1&rotation=0&showTitle=false&size=41707&status=done&style=none&taskId=u63d0aecb-150b-45f9-8b86-f7025f3a26e&title=&width=433.3333333333333)

> **vless 协议**


![image_tvu-3nyYPT.png](https://cdn.nlark.com/yuque/0/2022/png/22223333/1663739847184-030b7cac-c18f-417b-9e81-901553fe028d.png#clientId=u9a0ac102-2889-4&crop=0&crop=0&crop=1&crop=1&from=paste&height=489&id=uab3c41b0&name=image_tvu-3nyYPT.png&originHeight=734&originWidth=648&originalType=binary&ratio=1&rotation=0&showTitle=false&size=36902&status=done&style=none&taskId=ua319a2eb-b374-4288-814a-5fa10aeb116&title=&width=432)

> **Trojan 协议**


![image_nolrcfjnOv.png](https://cdn.nlark.com/yuque/0/2022/png/22223333/1663739854985-8731969d-0c62-44bb-84ba-3d0e90d930d4.png#clientId=u9a0ac102-2889-4&crop=0&crop=0&crop=1&crop=1&from=paste&height=451&id=ud4a17e2d&name=image_nolrcfjnOv.png&originHeight=676&originWidth=521&originalType=binary&ratio=1&rotation=0&showTitle=false&size=35192&status=done&style=none&taskId=u871cb2a2-1ae0-49fe-8fa6-682792b23e1&title=&width=347.3333333333333)

### 2.0.4 nginx配置

> **配置此项之前先搭建https静态网站(伪装)**

-  filebrowser (目前推荐安装此服务)
- 反代别人的网站
1. 安装nginx
```bash
dnf install nginx -y
```

2. 安装filebrowser
```bash
curl -fsSL https://raw.githubusercontent.com/filebrowser/get/master/get.sh | bash

#创建配置数据库
filebrowser -d /etc/filebrowser/filebrowser.db config init
#设置监听端口
filebrowser -d /etc/filebrowser/filebrowser.db config set --port 5210
#设置语言环境
filebrowser -d /etc/filebrowser/filebrowser.db config set --locale zh-cn
#添加一个用户
filebrowser -d /etc/filebrowser/filebrowser.db users add admin password --perm.admin
#设置网盘根目录
mkdir -p /data/fs
filebrowser -d /etc/filebrowser/filebrowser.db config set --root /data/fs

vim /lib/systemd/system/filebrowser.service

-----------------------------filebrowser.service------------
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
-----------------------------------------------------------

systemctl daemon-reload
systemctl start filebrowser
systemctl status filebrowser
systemctl enable filebrowser

用户名：admin
密码：password
```

3. 修改nginx配置
```bash
# 先删除nginx默认的80端口配置
vim /etc/nginx/nginx.conf 
```
确保http中有红框中的内容
![image.png](https://cdn.nlark.com/yuque/0/2022/png/22223333/1666335278942-d3347da3-56c6-4b5c-a679-dafd454141e9.png#clientId=u853fc3bb-8de9-4&crop=0&crop=0&crop=1&crop=1&from=paste&height=361&id=ua038a8fb&name=image.png&originHeight=541&originWidth=938&originalType=binary&ratio=1&rotation=0&showTitle=false&size=48787&status=done&style=none&taskId=u57563ebf-93d6-4d9b-b357-3bfeda3cf43&title=&width=625.3333333333334)
```bash
# 再添加新的配置
vim /etc/nginx/conf.d/vps.conf
```
```bash
server{
  ssl on;
  listen 443;
  server_name fs.gossip.tk;
  ssl_certificate /root/.acme.sh/fs.gossip.tk/fs.gossip.tk.cer;
  ssl_certificate_key /root/.acme.sh/fs.gossip.tk/fs.gossip.tk.key;
  ssl_session_timeout 5m;
  ssl_protocols TLSv1 TLSv1.1 TLSv1.2;
  ssl_ciphers ECDHE-RSA-AES128-GCM-SHA256:HIGH:!aNULL:!MD5:!RC4:!DHE;
  ssl_prefer_server_ciphers on;
  # 反向代理filebrowser网站
  location / {
    proxy_pass http://127.0.0.1:5210;
    # 设置文件上传大小
    client_max_body_size 100M;
    proxy_set_header Host $host;
    proxy_set_header X-Real-IP $remote_addr;
    proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
  }
  # x-ui面板
  location ^~ /cocoly {
    proxy_pass http://127.0.0.1:10105/cocoly;
    proxy_set_header Host $host;
    proxy_set_header X-Real-IP $remote_addr;
    proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
  }
  # ws协议
  location /gofic {
    proxy_redirect off;
    proxy_pass http://127.0.0.1:31694;
    proxy_http_version 1.1;
    proxy_set_header Upgrade $http_upgrade;
    proxy_set_header Connection "upgrade";
    proxy_set_header Host $http_host;
    proxy_read_timeout 300s;
    # Show realip in v2ray access.log
    proxy_set_header X-Real-IP $remote_addr;
    proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
  }

  # 可以把ws协议统一管理
  # include /etc/x-ui/location/*.conf;
}
```
> **可自行更改x-ui的根路径和端口（下面的内容应填在https配置的地方）**

```bash
location ^~ 面板url根路径 {
    proxy_pass http://127.0.0.1:面板监听端口/面板url根路径;
    proxy_set_header Host $host;
    proxy_set_header X-Real-IP $remote_addr;
    proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
}
location 节点路径 {
        proxy_redirect off;
        proxy_pass http://127.0.0.1:节点端口;
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection "upgrade";
        proxy_set_header Host $http_host;
        proxy_read_timeout 300s;
        # Show realip in v2ray access.log
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
}
```

ok之后可以到**cloudflare**开启小云朵代理
![image.png](https://cdn.nlark.com/yuque/0/2022/png/22223333/1666333123940-01291a6b-50ca-4587-aa18-eda597bc0592.png#clientId=u47599111-46d4-4&crop=0&crop=0&crop=1&crop=1&from=paste&height=185&id=u35becc8d&name=image.png&originHeight=277&originWidth=810&originalType=binary&ratio=1&rotation=0&showTitle=false&size=20405&status=done&style=none&taskId=ufc5a682d-cc2e-4fe0-b8f2-cecd90d0b5f&title=&width=540)
**将SSL的模式改为完全**，不然访问网站时可能会报** **此页面不能正确地重定向 错误
![image.png](https://cdn.nlark.com/yuque/0/2022/png/22223333/1666333105225-4e413502-0169-42ec-9a3f-04b3051f6798.png#clientId=u47599111-46d4-4&crop=0&crop=0&crop=1&crop=1&from=paste&height=478&id=u3ae4d906&name=image.png&originHeight=717&originWidth=1599&originalType=binary&ratio=1&rotation=0&showTitle=false&size=86025&status=done&style=none&taskId=u3de5db57-e9a1-492f-918d-62038bf9173&title=&width=1066)
### 我的配置
`vim /etc/nginx/conf.d/vps.conf`
> 动态伪装网站配置（搭建了filebrowser）

```nginx
server{
  ssl on;
  listen 443;
  server_name fs.gossip.tk;
  ssl_certificate /root/.acme.sh/fs.gossip.tk/fs.gossip.tk.cer;
  ssl_certificate_key /root/.acme.sh/fs.gossip.tk/fs.gossip.tk.key;
  ssl_session_timeout 5m;
  ssl_protocols TLSv1 TLSv1.1 TLSv1.2;
  ssl_ciphers ECDHE-RSA-AES128-GCM-SHA256:HIGH:!aNULL:!MD5:!RC4:!DHE;
  ssl_prefer_server_ciphers on;
  # 反向代理filebrowser网站
  location / {
    proxy_pass http://127.0.0.1:5210;
    # 设置文件上传大小
    client_max_body_size 100M;
    proxy_set_header Host $host;
    proxy_set_header X-Real-IP $remote_addr;
    proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
  }
  # x-ui面板
  location ^~ /cocoly {
    proxy_pass http://127.0.0.1:10105/cocoly;
    proxy_set_header Host $host;
    proxy_set_header X-Real-IP $remote_addr;
    proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
  }
  # ws协议
  location /gofic {
    proxy_redirect off;
    proxy_pass http://127.0.0.1:31694;
    proxy_http_version 1.1;
    proxy_set_header Upgrade $http_upgrade;
    proxy_set_header Connection "upgrade";
    proxy_set_header Host $http_host;
    proxy_read_timeout 300s;
    # Show realip in v2ray access.log
    proxy_set_header X-Real-IP $remote_addr;
    proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
  }

  # 可以把ws协议统一管理
  # include /etc/x-ui/location/*.conf;
}

# 停止80端口的使用，证书的申请需要占用80端口
#server {
#    listen 80;
#    server_name pan.gossip.tk;
#    rewrite ^(.*)$ https://$host$1 permanent;
#}
```
### 配置完成后重启nginx

```bash
# 检查nginx 配置的语法错误
nginx -t 
systemctl restart nginx

nginx -t 测试配置文件
nginx -s reload 修改配置后重载生效
nginx -s reopen 重新打开日志文件
nginx -s stop 快速停止
nginx -s quit
```

> 如果语法检查通过，重启报错的话，应该是端口被占用了


### 复制节点信息到代理软件中

> 注意勾选底层传输为tls（虽然节点并没有开启tls，但nginx已经转发到了443端口），并填写伪装域名


![image_W6nfMVdbmW.png](https://cdn.nlark.com/yuque/0/2022/png/22223333/1663740031040-86c1bd41-2e3d-4cfa-9618-e9ff429bd90e.png#clientId=u9a0ac102-2889-4&crop=0&crop=0&crop=1&crop=1&from=paste&height=521&id=u9a6efab2&name=image_W6nfMVdbmW.png&originHeight=781&originWidth=974&originalType=binary&ratio=1&rotation=0&showTitle=false&size=52630&status=done&style=none&taskId=ub4ba313f-69ae-42e1-b59f-608bc2f55da&title=&width=649.3333333333334)

### 开启cloudflare代理(选做)

![image_-kqqutxy86.png](https://cdn.nlark.com/yuque/0/2022/png/22223333/1663739938167-fd0bf0f7-23bf-4dac-ae40-894dcd731d49.png#clientId=u9a0ac102-2889-4&crop=0&crop=0&crop=1&crop=1&from=paste&height=455&id=u6fa6cbf9&name=image_-kqqutxy86.png&originHeight=682&originWidth=1883&originalType=binary&ratio=1&rotation=0&showTitle=false&size=113448&status=done&style=none&taskId=ua6292136-f8eb-46ea-b643-9a5eba76f3a&title=&width=1255.3333333333333)

![image_WemXjCyMEh.png](https://cdn.nlark.com/yuque/0/2022/png/22223333/1663739943187-9b1dcf27-f153-41a0-a718-855381665e9b.png#clientId=u9a0ac102-2889-4&crop=0&crop=0&crop=1&crop=1&from=paste&height=441&id=u3d1e6c5c&name=image_WemXjCyMEh.png&originHeight=661&originWidth=1366&originalType=binary&ratio=1&rotation=0&showTitle=false&size=82758&status=done&style=none&taskId=u9f6451c2-c609-4cf0-8b78-b4cc8766180&title=&width=910.6666666666666)

![image_W1EaUOWMXb.png](https://cdn.nlark.com/yuque/0/2022/png/22223333/1663739949121-87b94c31-3c2a-432f-abda-7b721ae82309.png#clientId=u9a0ac102-2889-4&crop=0&crop=0&crop=1&crop=1&from=paste&height=533&id=u4451923a&name=image_W1EaUOWMXb.png&originHeight=799&originWidth=1901&originalType=binary&ratio=1&rotation=0&showTitle=false&size=117501&status=done&style=none&taskId=u77176d96-7871-4c45-9a1a-42556d83f4c&title=&width=1267.3333333333333)

### 优选IP

软件下载地址

-  Github：[https://github.com/XIU2/CloudflareSpeedTest](https://github.com/XIU2/CloudflareSpeedTest) 
-  蓝奏云：[https://pan.lanzouo.com/b0742hkxe](https://pan.lanzouo.com/b0742hkxe) 

![image_1R0FL3p1Pb.png](https://cdn.nlark.com/yuque/0/2022/png/22223333/1663739974855-70203230-6308-4416-8e6c-d2369ccbb513.png#clientId=u9a0ac102-2889-4&crop=0&crop=0&crop=1&crop=1&from=paste&height=426&id=u1b4ac38c&name=image_1R0FL3p1Pb.png&originHeight=639&originWidth=1223&originalType=binary&ratio=1&rotation=0&showTitle=false&size=76816&status=done&style=none&taskId=ucccd5b77-fb24-4c0b-912b-1ee7d94cd42&title=&width=815.3333333333334)

**将优选出来的IP填入到之前的域名地址中，注意填写伪装域名**

![image_CgqlJZ5PO7.png](https://cdn.nlark.com/yuque/0/2022/png/22223333/1663739898039-1f9da24d-1d72-48c5-8961-7a2a7f7d49e3.png#clientId=u9a0ac102-2889-4&crop=0&crop=0&crop=1&crop=1&from=paste&height=521&id=iOvjv&name=image_CgqlJZ5PO7.png&originHeight=781&originWidth=974&originalType=binary&ratio=1&rotation=0&showTitle=false&size=50909&status=done&style=none&taskId=ue904fbe0-34b7-42f5-b07f-d02fdb9a4c7&title=&width=649.3333333333334)

**成功之后可对节点进行测速，对比之前的0.4M/s快了很多**

![image_ACPWbOtWlN.png](https://cdn.nlark.com/yuque/0/2022/png/22223333/1663739987055-46e5e4da-ebb4-4b94-b8ee-3442ae6ad3ca.png#clientId=u9a0ac102-2889-4&crop=0&crop=0&crop=1&crop=1&from=paste&height=211&id=u9baae69e&name=image_ACPWbOtWlN.png&originHeight=317&originWidth=1037&originalType=binary&ratio=1&rotation=0&showTitle=false&size=31762&status=done&style=none&taskId=u8b4a3210-e3d1-4a28-8e6d-a53e76dd27d&title=&width=691.3333333333334)
