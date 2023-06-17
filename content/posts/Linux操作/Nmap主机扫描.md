---
title: "Nmap主机扫描"
description: "Nmap主机扫描"
keywords: "Nmap"

date: 2023-06-16T16:41:30+08:00
lastmod: 2023-06-16T16:41:30+08:00

categories:
  - Linux
tags:
  - Linux
  - 工具使用
---

## ICMP协议探测
```bash
nmap -sn -PE -T4 192.168.5.0/24 
```
## ARP协议探测
```bash
nmap -sn -PR 192.168.5.0/24
```
