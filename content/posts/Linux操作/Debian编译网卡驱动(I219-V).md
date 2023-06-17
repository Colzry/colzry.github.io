---
title: "Debian编译网卡驱动(I219-V)"
description: "Debian编译网卡驱动(I219-V)"
keywords: "驱动"

date: 2023-06-16T16:41:30+08:00
lastmod: 2023-06-16T16:41:30+08:00

categories:
  - Linux
tags:
  - Linux
  - 疑难杂症
---

## 换源
```bash
bash <(curl -sSL https://gitee.com/SuperManito/LinuxMirrors/raw/main/ChangeMirrors.sh)
```
## 1. 下载网卡驱动
下载e1000e网卡驱动，下载地址
https://downloadcenter.intel.com/zh-cn/download/15817?_ga=1.159975677.114505945.1484457019
## 2. 配置编译环境
```bash
# 查看内核版本
uname -r
# 去华农镜像下载对应的linux-headers
curl -O https://mirrors.scau.edu.cn/proxmox/debian/dists/bullseye/pve-no-subscription/binary-amd64/pve-headers-5.15.30-2-pve_5.15.30-3_amd64.deb
# 安装linux-headers
dpkg -i pve-headers-5.15.30-2-pve_5.15.30-3_amd64.deb
# 安装编译工具链
sudo apt install build-essential
```
> build-essential 包含了以下编译环境

```bash
$ apt-cache depends build-essential
build-essential
 |Depends: libc6-dev
  Depends: <libc-dev>
    libc6-dev
  Depends: gcc
  Depends: g++
  Depends: make
    make-guile
  Depends: dpkg-de

RedHat 安装
yum install make automake gcc gcc-c++ kernel-devel
```
## 3. 开始编译
```bash
cd e1000e-3.8.5/src
make install
```
> 若编译报错

```bash
common.mk:82: *** Kernel header files not in any of the expected locations.
common.mk:83: *** Install the appropriate kernel development package, e.g.
common.mk:84: *** kernel-devel, for building kernel modules and try again.  Stop.
```
安装内核源码
```bash
sudo apt-get install linux-headers-$(uname -r)
```
> 若上面的安装失败，安装通用内核替代

```bash
sudo apt-get install linux-headers-generic
```
创建软连接
```bash
ln -s /usr/src/linux-headers-5.4.0-65-generic /usr/src/linux
```
