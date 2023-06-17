---
title: "SSH防暴力破解"
description: "SSH防暴力破解"
keywords: "SSH"

date: 2023-06-16T16:41:30+08:00
lastmod: 2023-06-16T16:41:30+08:00

categories:
  - SSH
tags:
  - SSH
---

## 1. 添加登录试错次数
```bash
vim /etc/pam.d/sshd

# 在末尾添加以下内容
auth required pam_tally2.so deny=3 unlock_time=60 even_deny_root root_unlock_time=60

# 解释
even_deny_root 也限制root用户；  
deny 设置普通用户和root用户连续错误登陆的最大次数，超过最大次数，则锁定该用户  
unlock_time 设定普通用户锁定后，多少时间后解锁，单位是秒；  
root_unlock_time 设定root用户锁定后，多少时间后解锁，单位是秒；
```

> 解除锁定方法

```bash
# 在服务器端以root用户执行命令, 清空指定用户的错误登录次数
pam_tally2 -u root -r  
# 或者  
pam_tally2 --user 用户名 --reset  
```

## 2. 防火墙拉黑攻击IP
> 查看登录的IP

```bash
# 查看登入成功的用户日志
last -f /var/run/utmp

# 查看登入失败的用户日志
last -f /var/log/btmp
```
> 使用防火墙封禁IP

```bash
# 单个IP
firewall-cmd --permanent --add-rich-rule="rule family='ipv4' source address='46.216.73.28' reject"

# IP段
firewall-cmd --permanent --add-rich-rule="rule family='ipv4' source address='46.216.73.0/24' reject"

# 单个IP的某个端口
firewall-cmd --permanent --add-rich-rule="rule family=ipv4 source address=46.216.73.28 port port=80  protocol=tcp  reject"

#  reject 拒绝   accept 允许
```
> 重新载入以生效

```bash
firewall-cmd --reload
```
> 查看封禁IP

```bash
firewall-cmd --list-rich-rules
```