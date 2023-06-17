---
title: "iperf3的使用"
description: "iperf3的使用"
keywords: "iperf3"

date: 2023-06-16T16:41:30+08:00
lastmod: 2023-06-16T16:41:30+08:00

categories:
  - Linux
tags:
  - Linux
  - 工具使用
---



## 1. 安装

### 1.1 linux
```bash
# Debian
sudo apt install iperf3 -y 
# Centos
sudo yum install iperf3 -y
```
### 1.2 windows
[官网下载地址](https://iperf.fr/iperf-download.php)
![image.png](https://cdn.nlark.com/yuque/0/2022/png/22223333/1657618535777-badffd80-4577-4916-8530-09a667655d4a.png#clientId=ua25e1854-f97e-4&crop=0&crop=0&crop=1&crop=1&from=paste&height=583&id=ucc60ef01&margin=%5Bobject%20Object%5D&name=image.png&originHeight=583&originWidth=1795&originalType=binary&ratio=1&rotation=0&showTitle=false&size=123536&status=done&style=none&taskId=ud7b1ff27-deab-4f1b-ab20-93befe4403d&title=&width=1795)
## 2. 使用
### 2.1 详细命令参数
```bash
-p, --port #，Server 端监听、Client 端连接的端口号； 
-f, --format [kmgKMG]，报告中所用的数据单位，Kbits, Mbits, KBytes, Mbytes； 
-i, --interval #，每次报告的间隔，单位为秒； 
-F, --file name，测试所用文件的文件名。如果使用在 Client 端，发送该文件用作测试；如果使用在 Server 端，则是将数据写入该文件，而不是丢弃； 
-A, --affinity n/n,m，设置 CPU 亲和力； 
-B, --bind ，绑定指定的网卡接口； 
-V, --verbose，运行时输出更多细节； 
-J, --json，运行时以 JSON 格式输出结果； 
--logfile f，输出到文件； 
-d, --debug，以 debug 模式输出结果； 
-v, --version，显示版本信息并退出； 
-h, --help，显示帮助信息并退出。 
Server 端参数： 
-s, --server，以 Server 模式运行； 
-D, --daemon，在后台以守护进程运行； 
-I, --pidfile file，指定 pid 文件； 
-1, --one-off，只接受 1 次来自 Client 端的测试，然后退出。 
Client 端参数 
-c, --client ，以 Client 模式运行，并指定 Server 端的地址； 
-u, --udp，以 UDP 协议进行测试； 
-b, --bandwidth #[KMG][/#]，限制测试带宽。UDP 默认为 1Mbit/秒，TCP 默认无限制； 
-t, --time #，以时间为测试结束条件进行测试，默认为 10 秒； 
-n, --bytes #[KMG]，以数据传输大小为测试结束条件进行测试； 
-k, --blockcount #[KMG]，以传输数据包数量为测试结束条件进行测试； 
-l, --len #[KMG]，读写缓冲区的长度，TCP 默认为 128K，UDP 默认为 8K； 
--cport ，指定 Client 端运行所使用的 TCP 或 UDP 端口，默认为临时端口； 
-P, --parallel #，测试数据流并发数量； 
-R, --reverse，反向模式运行（Server 端发送，Client 端接收）； 
-w, --window #[KMG]，设置套接字缓冲区大小，TCP 模式下为窗口大小； 
-C, --congestion ，设置 TCP 拥塞控制算法（仅支持 Linux 和 FreeBSD ）； 
-M, --set-mss #，设置 TCP/SCTP 最大分段长度（MSS，MTU 减 40 字节）； 
-N, --no-delay，设置 TCP/SCTP no delay，屏蔽 Nagle 算法； 
-4, --version4，仅使用 IPv4； 
-6, --version6，仅使用 IPv6； 
-S, --tos N，设置 IP 服务类型（TOS，Type Of Service）； 
-L, --flowlabel N，设置 IPv6 流标签（仅支持 Linux）； 
-Z, --zerocopy，使用 “zero copy”（零拷贝）方法发送数据； 
-O, --omit N，忽略前 n 秒的测试； 
-T, --title str，设置每行测试结果的前缀； 
--get-server-output，从 Server 端获取测试结果； 
--udp-counters-64bit，在 UDP 测试包中使用 64 位计数器（防止计数器溢出）。 
```
### 2.2 简单使用例子
```bash
# 示例1 正向测试
# Server
iperf3 -s
# Client
iperf3 -c Server_IP地址

# 示例2 反向测试
iperf3 -s
# Client
iperf3 -c Server_IP地址 -R

# 示例3
# Server
iperf3 -s
# Client
iperf3 -c Server_IP地址 -b 1000M -t 60 -d

-c 为客户端运行并要指定服务端的IP地址
-b 表示使用的测试带宽
-t 表示以时间为测试结束条件进行测试，默认为 10 秒
-d 打印出更详细的debug调试信息
```
