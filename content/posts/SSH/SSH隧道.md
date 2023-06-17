---
title: "SSH隧道"
description: "SSH隧道"
keywords: "隧道"

date: 2023-06-16T16:41:30+08:00
lastmod: 2023-06-16T16:41:30+08:00

categories:
  - SSH
tags:
  - 隧道
---

## 先总结
- 如果源地址是 ssh client 的某个端口，称为本地转发（Local Port Forwarding），发往 ssh client 指定端口的数据包会经过 ssh server 进行转发；
- 如果源地址是 ssh server 的某个端口，则称为远程转发（Remote Port Forwarding），发往 ssh server 指定端口的数据包会经过 ssh client 进行转发.

![1-19.jpg](https://cdn.nlark.com/yuque/0/2022/jpeg/22223333/1659533267293-f3a52543-58a1-4f93-b123-6329c37a8332.jpeg#clientId=uf7a49369-0c5a-4&crop=0&crop=0&crop=1&crop=1&from=paste&height=474&id=u122a6295&margin=%5Bobject%20Object%5D&name=1-19.jpg&originHeight=711&originWidth=929&originalType=binary&ratio=1&rotation=0&showTitle=false&size=39287&status=done&style=shadow&taskId=uc46d3f64-2352-4247-abf4-ec8adddf7ec&title=&width=619.3333333333334)
![ssh-port-forwarding.webp](https://cdn.nlark.com/yuque/0/2022/webp/22223333/1659533276092-5ebf0365-5456-4662-80a8-82920d8928cd.webp#clientId=uf7a49369-0c5a-4&crop=0&crop=0&crop=1&crop=1&from=paste&height=379&id=uc0f537c4&margin=%5Bobject%20Object%5D&name=ssh-port-forwarding.webp&originHeight=390&originWidth=543&originalType=binary&ratio=1&rotation=0&showTitle=false&size=25242&status=done&style=shadow&taskId=uc0c9ec5b-1a1e-40a0-a046-4349eaae0e3&title=&width=528)
```bash
# 注意：命令都是在本地client上运行

# 本地端口转发
# 访问本地端口即访问目标端口
ssh -L [本地ip]:本地端口:远程ip:远程端口 user@server_ip

# 远程端口转发
# 访问远程端口即访问目标端口
ssh -R [远程ip]:远程端口:本地ip:本地端口 user@server_ip
```

## 注意
> **1. 使用ssh前先修改配置文件**

![image-20230421140114499](https://picture-czy.oss-cn-beijing.aliyuncs.com/img/image-20230421140114499.png)

> **2. 远程转发本地的地址填localhost不要填127.0.0.1**

## 命令大纲
**假设存在三个设备**

- laptop 笔记本
- laptop_app 与笔记本同内网的设备（远程服务器不能连接）ip: 172.14.17.28
- server 远程服务器  ip: 47.28.101.27
- server_app 与远程服务器同内网的设备（笔记本不能连接）ip: 172.22.89.30

**以下命令都在笔记本终端上运行**
```bash
# 本地端口转发 (访问本地端口即访问目标`server_app`上的端口)
ssh -N -g -f -L 8080:172.22.89.30:80 root@47.28.101.27

-N 只进行ssh隧道转发，不连接ssh
-g 表示源ip为0.0.0.0
-f 后台运行

# 远程端口转发 (访问远程端口即访问目标`laptop_app`上的端口)
ssh -N -g -f -R 80:172.14.17.28:3000 root@47.28.101.27

# 动态转发
ssh -N -g -f -D 1080 root@47.28.101.27
得到socks5代理服务
```
## 1. 本地端口转发
绿色代表能访问，红色代表不能访问
![image.png](https://cdn.nlark.com/yuque/0/2022/png/22223333/1659531888307-ddde49a8-20f2-494b-b173-9b3f4860feec.png#clientId=uf7a49369-0c5a-4&crop=0&crop=0&crop=1&crop=1&from=paste&height=391&id=u481d173b&margin=%5Bobject%20Object%5D&name=image.png&originHeight=586&originWidth=1017&originalType=binary&ratio=1&rotation=0&showTitle=false&size=58199&status=done&style=shadow&taskId=u65e9c916-8aff-43a7-a01c-9e94f0a5879&title=&width=678)
```bash
ssh -N -g -f -L 8080:172.22.89.30:80 root@47.28.101.27
# frp实战（即访问本地8080端口等价访问目标80端口）
ssh -N -g -L 8080:127.0.0.1:80 root@cn-hz-bgp-1.openfrp.top -p 43252
```
访问**本机主机**的8080端口即访问**目标主机**的80端口服务
## 2. 远程端口转发
![image.png](https://cdn.nlark.com/yuque/0/2022/png/22223333/1659531801851-5d748122-7b8b-4558-a3f2-7fe0f2e21692.png#clientId=uf7a49369-0c5a-4&crop=0&crop=0&crop=1&crop=1&from=paste&height=409&id=u0deb5ccd&margin=%5Bobject%20Object%5D&name=image.png&originHeight=613&originWidth=1032&originalType=binary&ratio=1&rotation=0&showTitle=false&size=59087&status=done&style=shadow&taskId=u22efe391-b4a2-4be7-9cb3-2c3395ee6ec&title=&width=688)
```bash
ssh -N -g -f -R 80:172.14.17.28:3000 root@47.28.101.27
# 腾讯云实战（访问http://43.142.174.216:5200/ 即访问目标的 5212端口上的服务）
ssh -N -g -R 5200:192.168.5.58:5212 colzry@43.142.174.216
```
访问**远程主机**的端口即访问**目标主机**的端口服务
常用于将内部的服务让其他用户访问

关闭服务
```bash
ps -ef | grep ssh
kill -9 PID
```
## 3. 动态端口转发
相当于代理服务器，即科学上网的节点
```bash
ssh -N -g -f -D 1080 root@47.28.101.27
```
socks5://127.0.0.1:1080
使用示例：linux-debian系列
```bash
sudo apt-get install proxychains -y

vim /etc/proxychains.conf
# 尾部加上
socks5  127.0.0.1 1080

# 在需要使用代理命令前使用
proxychains git clone https://github.com/xxx/xxx
```
