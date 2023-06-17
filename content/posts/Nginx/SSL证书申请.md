---
title: "SSL证书申请"
description: "SSL证书申请"
keywords: "Nginx"

date: 2023-06-16T16:41:30+08:00
lastmod: 2023-06-16T16:41:30+08:00

categories:
  - Nginx
tags:
  - Nginx
---

## 1. Acme 脚本申请证书

Acme 脚本申请证书，是我们用到的最常见的一种证书的申请方式，它有很多的申请方法，大家只需要找到一种适合自己的也就好了。

不管用下面的何种方式申请，都需要安装 Acme，有一部分的申请场景需要用到相关的插件，所以我们需要提前安装。
下面环境的安装方式，大家根据自己的系统选择命令安装就好了。
```shell
apt update -y          #Debian/Ubuntu 命令
apt install -y curl    #Debian/Ubuntu 命令
apt install -y socat    #Debian/Ubuntu 命令
yum update -y          #CentOS 命令
yum install -y curl    #CentOS 命令
yum install -y socat    #CentOS 命令

# 关闭防火墙
systemctl disable firewalld.service
systemctl stop firewalld.service
```

安装 Acme 脚本
```shell
curl https://get.acme.sh | sh
```
更换Acme的默认CA颁发机构
```bash
~/.acme.sh/acme.sh --set-default-ca --server letsencrypt
```
## 2. 申请时请先解析域名
### 2.1 80 端口空闲的验证申请(到此可以结束)
如果你还没有运行任何 web 服务, 80 端口是空闲的, 那么 Acme.sh 还能假装自己是一个 WebServer, 临时监听在 80 端口, 完成验证
```shell
~/.acme.sh/acme.sh  --issue -d 填入你的域名 --standalone
```

### 2.2 指定空闲端口申请
如果80端口用不了，可以指定其他的端口
```shell
~/.acme.sh/acme.sh  --issue -d 填入你的域名 --standalone --httpport 88
```

### 2.3 Nginx 的方式验证申请
这种方式需要你的服务器上面已经部署了 Nginx 环境，并且保证你申请的域名已经在 Nginx 进行了 conf 部署。（被申请的域名可以正常被打开）
```shell
~/.acme.sh/acme.sh --issue -d 填入你的域名 --nginx
```

### 2.4 http 的方式验证申请
这种方式需要你的服务器上面已经部署了网站环境。（被申请的域名可以正常被打开）
原理：Acme 自动在你的网站根目录下放置一个文件, （这个文件可以被互联网访问）来验证你的域名所有权,完成验证. 然后就可以生成证书了.
实例代码：（后面的路径请更改为你的 `网站根目录` `绝对路径` ）
```shell
~/.acme.sh/acme.sh  --issue  -d 填入你的域名  --webroot  /home/wwwroot/网站目录/
```

### 2.5 安装证书到指定文件夹
注意, 默认生成的证书都放在安装目录下: ~/.acme.sh/, 请不要直接使用此目录下的证书文件。
正确的使用方法是使用 `--install-cert` 命令,并指定目标位置, 然后证书文件会被copy到相应的位置，比如下面的代码
```shell
~/.acme.sh/acme.sh --installcert -d 填入你的域名 --key-file /root/private.key --fullchain-file /root/cert.crt
```
上面的 `/root/private.key` 以及 `/root/cert.crt` 是把密钥和证书安装到 `/root` 目录，并改名为 `private.key` 和 `cert.crt`

### 2.6 更新证书
目前证书在 `60` 天以后会自动更新, 你无需任何操作. 今后有可能会缩短这个时间, 不过都是自动的, 你不用关心.

#### 2.6.1 查看更新证书列表
```bash
/root/.acme.sh/acme.sh --list
```

#### 2.6.2 手动更新所有证书
```bash
/root/.acme.sh/acme.sh --renew-all
```

#### 2.6.3 手动更新指定证书
```bash
/root/.acme.sh/acme.sh --renew -d 域名
```

#### 2.6.4 停止自动更新
```bash
/root/.acme.sh/acme.sh --remove -d 要停止的域名
```


### 2.7 更新 Acme 脚本
升级 Acme.sh 到最新版本
```shell
~/.acme.sh/acme.sh --upgrade
```
如果你不想手动升级, 可以开启自动升级:
```shell
~/.acme.sh/acme.sh  --upgrade  --auto-upgrade
```
之后, acme.sh 就会自动保持更新了.
