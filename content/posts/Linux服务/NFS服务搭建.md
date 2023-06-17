---
title: "NFS服务搭建"
description: "NFS服务搭建"
keywords: "NFS"

date: 2023-06-16T16:41:30+08:00
lastmod: 2023-06-16T16:41:30+08:00

categories:
  - Linux
tags:
  - Linux
  - 服务搭建
  - NFS
---

### 1.安装服务端

```bash
# Debian
sudo apt install nfs-kernel-server
# RedHat
sudo yum install rpcbind nfs-utils
```

### 2. 更改配置文件

```bash
sudo vim /etc/exports
```

填入一下内容

```bash
# * 表示允许任何网段 IP 的系统访问该 NFS 目录
/nfs *(rw,sync,no_root_squash)
```

> 配置说明

```bash
NFS配置参数权限： 
ro 只读访问 
rw 读写访问 
---------------
sync 同步方式存储数据直接将数据保存到磁盘（数据存储安全）
async 异步方式存储数据直接将数据保存到内存（提高数据存储效率）
all_squash 将所有用户身份都进行转换匿名用户anonymous，适合公用目录。 
no_all_squash 不要将普通用户身份进行转换
root_squash 将root用户身份进行转换,root用户的所有请求映射成如anonymous用户一样的权限（默认） 
no_root_squas 不要将root用户身份进行转换
```

### 3.创建目录并赋予权限

```bash
sudo mkdir /nfs
sudo chown nobody:nogroup /nfs
sudo chmod -R 777 /nfs
```

### 4.启动NFS服务

```bash
sudo /etc/init.d/nfs-kernel-server restart

或者

systemctl restart nfs-kernel-server.service
```

### 5. 写入测试内容

```bash
echo "test" >> /nfs/test.txt
```

> **到此服务器端的安装配置完毕**


### 6.客户端操作

安装客户端

```bash
sudo apt install nfs-common
```

挂载

```bash
sudo mount -t nfs 192.168.5.103:/nfs /mnt/nfs -o nolock
```

设置开机自动挂载

```bash
sudo vim /etc/fstab


192.168.5.103:/nfs   /mnt/nfs   nfs  rw 0 0
```
