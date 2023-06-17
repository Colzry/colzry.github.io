---
title: "Linux防火墙和Cockpit"
description: "Linux防火墙和Cockpit"
keywords: "防火墙"

date: 2023-06-16T16:41:30+08:00
lastmod: 2023-06-16T16:41:30+08:00

categories:
  - Linux
tags:
  - Linux
---

## 1. firewall (RedHat系列)
```bash
# 查看状态
firewall-cmd --state

# 开启防火墙
systemctl start firewalld.service

# 关闭并禁用防火墙
systemctl stop firewalld.service
systemctl disable firewalld.service

# 关闭SElinux 该为disable
vim /etc/selinux/config


# 开放端口
firewall-cmd --zone=public --add-port=8080/tcp --permanent
firewall-cmd --reload

# 关闭端口
firewall-cmd --zone=public --remove-port=8080/tcp --permanent
firewall-cmd --reload

# 查看以开放的端口
firewall-cmd --list-ports

# 更新防火墙规则
firewall-cmd --reload
```

## 2. ufw (Ubuntu)
```bash
# 查看状态
ufw status


```
## 3. Cockpit安装
```bash
dnf install cockpit cockpit-dashboard /
cockpit-storaged cockpit-packagekit -y

# 也可以安装其它的扩展包
dnf list cockpit*
--------------------------
cockpit-bridge.x86_64
cockpit-composer.noarch
cockpit-doc.noarch       
cockpit-machines.noarch    
cockpit-packagekit.noarch   
cockpit-pcp.x86_64          
cockpit-podman.noarch       
cockpit-session-recording.noarch
cockpit-storaged.noarch       
cockpit-system.noarch            
cockpit-ws.x86_64  
--------------------------


# 启动cockpit并设为开启自启动
systemctl enable --now cockpit.socket /
&& systemctl list-unit-files | grep cockpit /
&& systemctl start cockpit

# 有防火墙的话，记得开放
firewall-cmd --permanent --zone=public --add-service=cockpit
firewall-cmd --reload

之后访问本机9090端口
```

