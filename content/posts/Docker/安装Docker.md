---
title: "安装Docker"
date: 2023-06-16T10:10:26+08:00
categories:
 - Docker
tags:
  - Docker
---


## 1. 命令行安装
### 1.1 卸载旧版本
```bash
yum remove docker \
	docker-client \
	docker-client-latest \
	docker-common \
	docker-latest \
	docker-latest-logrotate \
	docker-logrotate \
	docker-selinux \
	docker-engine-selinux \
	docker-engine
```

### 1.2 安装
执行以下命令安装依赖包：
```bash
yum install -y yum-utils
```
鉴于国内网络问题，强烈建议使用国内源，官方源请在注释中查看。
执行下面的命令添加 `yum` 软件源：
```bash
yum-config-manager \
--add-repo \
https://mirrors.aliyun.com/docker-ce/linux/centos/docker-ce.repo

# 官方源
# yum-config-manager \
# --add-repo \
# https://download.docker.com/linux/centos/docker-ce.repo
```
更新 `yum` 软件源缓存，并安装 `docker-ce`
```bash
yum makecache

yum install docker-ce docker-ce-cli containerd.io docker-scan-plugin docker-compose-plugin docker-ce-rootless-extras
```

### 1.3 防火墙额外设置
由于 CentOS8 防火墙使用了 `nftables`，但 Docker 尚未支持 `nftables`， 我们可以使用如下设置使用 `iptables`：

更改 `vim /etc/firewalld/firewalld.conf`
```bash
# FirewallBackend=nftables
FirewallBackend=iptables
```
或者执行如下命令：
```bash
firewall-cmd --permanent --zone=trusted --add-interface=docker0
firewall-cmd --reload
```

## 2. 使用脚本自动安装
```bash
curl -fsSL https://get.docker.com | bash -s docker --mirror Aliyun
```

## 3. 启动Docker
```bash
systemctl enable docker
systemctl start docker
```