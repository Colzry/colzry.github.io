---
title: "DNS服务列表"
description: "DNS服务列表"
keywords: "DNS"

date: 2023-06-16T16:41:30+08:00
lastmod: 2023-06-16T16:41:30+08:00

categories:
  - Other
tags:
  - DNS
---

阿里：
```bash
IPv4 DNS 地址:
223.5.5.5
223.6.6.6
IPv6 DNS 地址：
2400:3200::1
2400:3200:baba::1
```
腾讯：
```bash
119.29.29.29
119.28.28.28
```
114:
```bash
114.114.114.114
114.114.115.115
```

Linux 配置DNS
```bash
sudo vim /etc/resolv.conf

nameserver 223.6.6.6
```

### Linux 换源
```bash
bash <(curl -sSL https://gitee.com/SuperManito/LinuxMirrors/raw/main/ChangeMirrors.sh)
```

