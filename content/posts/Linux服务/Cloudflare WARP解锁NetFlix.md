---
title: "Cloudflare WARP解锁NetFlix"
description: "Cloudflare WARP解锁NetFlix"
keywords: "WARP"

date: 2023-06-16T16:41:30+08:00
lastmod: 2023-06-16T16:41:30+08:00

categories:
  - Linux
tags:
  - Linux
  - 服务搭建
---

## 1. 检测是否解锁
```bash
#项目地址：https://github.com/sjlleo/netflix-verify

#下载检测解锁程序
wget -O nf https://github.com/sjlleo/netflix-verify/releases/download/v3.1.0/nf_linux_amd64 && chmod +x nf

#执行
./nf

#通过代理执行
./nf -proxy socks5://127.0.0.1:40000
```
## 2. WARP安装
github地址：[https://github.com/P3TERX/warp.sh](https://github.com/P3TERX/warp.sh)
使用文档地址：[https://p3terx.com/archives/cloudflare-warp-configuration-script.html](https://p3terx.com/archives/cloudflare-warp-configuration-script.html)
使用以下命令一把梭后将自动安装 WARP 官方客户端并开启 SOCKS5 代理端口 (127.0.0.1:40000)：
```bash
# 自动配置 WARP 官方客户端 SOCKS5 代理
bash <(curl -fsSL git.io/warp.sh) s5
```
执行以下命令显示功能菜单和贴心的状态显示：
```bash
# Cloudflare WARP 一键配置脚本 功能菜单
bash <(curl -fsSL git.io/warp.sh) menu
```
![image.png](https://cdn.nlark.com/yuque/0/2022/png/22223333/1663909816055-fbaed518-9d6b-4895-b192-07cd5c4e3ea5.png#clientId=uff796677-aabc-4&crop=0&crop=0&crop=1&crop=1&from=paste&height=537&id=u55c0cd6f&margin=%5Bobject%20Object%5D&name=image.png&originHeight=806&originWidth=811&originalType=binary&ratio=1&rotation=0&showTitle=false&size=77281&status=done&style=none&taskId=u322c49e8-b2f3-4f2b-87d2-e42cb362ae0&title=&width=540.6666666666666)
## 3. 通过WARP代理再次检测是否解锁
```bash
./nf -proxy socks5://127.0.0.1:40000

#查询代理后的IP地址：
curl ifconfig.me --proxy socks5://127.0.0.1:40000
```
![image.png](https://cdn.nlark.com/yuque/0/2022/png/22223333/1663909898052-fe1cce3c-02cb-42be-9565-8e82e1f8910b.png#clientId=uff796677-aabc-4&crop=0&crop=0&crop=1&crop=1&from=paste&height=157&id=uf8b8240d&margin=%5Bobject%20Object%5D&name=image.png&originHeight=235&originWidth=710&originalType=binary&ratio=1&rotation=0&showTitle=false&size=31346&status=done&style=none&taskId=uee0a0fb9-2fc9-409c-95ee-2363e54916c&title=&width=473.3333333333333)
## 4. 配置Xray分流
替换掉文本域中的内容后，重启面板
![image.png](https://cdn.nlark.com/yuque/0/2022/png/22223333/1663909968463-24425585-2480-4ded-a0e1-a6f586b5dc08.png#clientId=uff796677-aabc-4&crop=0&crop=0&crop=1&crop=1&from=paste&height=373&id=u3b84c5ef&margin=%5Bobject%20Object%5D&name=image.png&originHeight=559&originWidth=2240&originalType=binary&ratio=1&rotation=0&showTitle=false&size=54719&status=done&style=none&taskId=u328205c4-272a-4d30-b0d4-2eb40aeaade&title=&width=1493.3333333333333)
```json
{
  "api": {
    "services": [
      "HandlerService",
      "LoggerService",
      "StatsService"
    ],
    "tag": "api"
  },
  "inbounds": [
    {
      "listen": "127.0.0.1",
      "port": 62789,
      "protocol": "dokodemo-door",
      "settings": {
        "address": "127.0.0.1"
      },
      "tag": "api"
    }
  ],
  "outbounds": [
    {
      "protocol": "freedom",
      "settings": {}
    },
    {
      "tag": "netflix_proxy",
      "protocol": "socks",
      "settings": {
        "servers": [
          {
            "address": "127.0.0.1",
            "port": 40000
          }
        ]
      }
    },
    {
      "protocol": "blackhole",
      "settings": {},
      "tag": "blocked"
    }
  ],
  "policy": {
    "system": {
      "statsInboundDownlink": true,
      "statsInboundUplink": true
    }
  },
  "routing": {
    "rules": [
      {
        "type": "field",
        "outboundTag": "netflix_proxy",
        "domain": [
          "geosite:netflix",
          "geosite:disney",
          "geosite:category-porn"
        ]
      },
      {
        "inboundTag": [
          "api"
        ],
        "outboundTag": "api",
        "type": "field"
      },
      {
        "ip": [
          "geoip:private"
        ],
        "outboundTag": "blocked",
        "type": "field"
      },
      {
        "outboundTag": "blocked",
        "protocol": [
          "bittorrent"
        ],
        "type": "field"
      }
    ]
  },
  "stats": {}
}
```
重启面板后不通过代理再次检测
```bash
./nf
```
![image.png](https://cdn.nlark.com/yuque/0/2022/png/22223333/1663910109504-13aefda5-ccbf-45c7-a74b-fdd020ab8d8d.png#clientId=uff796677-aabc-4&crop=0&crop=0&crop=1&crop=1&from=paste&height=155&id=u44e8b389&margin=%5Bobject%20Object%5D&name=image.png&originHeight=233&originWidth=525&originalType=binary&ratio=1&rotation=0&showTitle=false&size=27623&status=done&style=none&taskId=uc4cd67b6-bfc1-45ee-b393-3692241b84f&title=&width=350)



原来的xray配置
```json
{
  "api": {
    "services": [
      "HandlerService",
      "LoggerService",
      "StatsService"
    ],
    "tag": "api"
  },
  "inbounds": [
    {
      "listen": "127.0.0.1",
      "port": 62789,
      "protocol": "dokodemo-door",
      "settings": {
        "address": "127.0.0.1"
      },
      "tag": "api"
    }
  ],
  "outbounds": [
    {
      "protocol": "freedom",
      "settings": {}
    },
    {
      "protocol": "blackhole",
      "settings": {},
      "tag": "blocked"
    }
  ],
  "policy": {
    "system": {
      "statsInboundDownlink": true,
      "statsInboundUplink": true
    }
  },
  "routing": {
    "rules": [
      {
        "inboundTag": [
          "api"
        ],
        "outboundTag": "api",
        "type": "field"
      },
      {
        "ip": [
          "geoip:private"
        ],
        "outboundTag": "blocked",
        "type": "field"
      },
      {
        "outboundTag": "blocked",
        "protocol": [
          "bittorrent"
        ],
        "type": "field"
      }
    ]
  },
  "stats": {}
}
```
