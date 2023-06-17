---
title: "Centos8 KVM 虚拟化"
description: "Centos8 KVM 虚拟化"
keywords: "KVM"

date: 2023-06-16T16:41:30+08:00
lastmod: 2023-06-16T16:41:30+08:00

categories:
  - KVM
tags:
  - KVM
  - Linux
---

## 1. 安装Centos8

### 1.1 下载 Centos8 镜像

下载地址：[http://isoredirect.centos.org/centos/8/isos/x86_64/](http://isoredirect.centos.org/centos/8/isos/x86_64/)

### 1.2 安装Centos8

**1.2.1 回车**

![](https://picture-czy.oss-cn-beijing.aliyuncs.com/homeWinDir/image-20210919154635809.png#crop=0&crop=0&crop=1&crop=1&id=fiMEi&originHeight=496&originWidth=638&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

**1.2.2 选择中文**

![](https://picture-czy.oss-cn-beijing.aliyuncs.com/homeWinDir/image-20210919154831377.png#crop=0&crop=0&crop=1&crop=1&id=K37jw&originHeight=619&originWidth=835&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

**1.2.3 从右到左依次点击配置，先选择安装目的地**

![](https://picture-czy.oss-cn-beijing.aliyuncs.com/homeWinDir/image-20210919160114130.png#crop=0&crop=0&crop=1&crop=1&id=HWk0v&originHeight=601&originWidth=810&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

![](https://picture-czy.oss-cn-beijing.aliyuncs.com/homeWinDir/image-20210919160148233.png#crop=0&crop=0&crop=1&crop=1&id=xXEGk&originHeight=627&originWidth=839&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

**1.2.4 配置网络**

![](https://picture-czy.oss-cn-beijing.aliyuncs.com/homeWinDir/image-20210919160247258.png#crop=0&crop=0&crop=1&crop=1&id=vgGaP&originHeight=602&originWidth=803&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

![](https://picture-czy.oss-cn-beijing.aliyuncs.com/homeWinDir/image-20210919161421145.png#crop=0&crop=0&crop=1&crop=1&id=pN6go&originHeight=594&originWidth=800&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

这时要等待镜像源的选择，不要认为卡了

**1.2.5 来到软件选择，选择安装虚拟化主机**

![](https://picture-czy.oss-cn-beijing.aliyuncs.com/homeWinDir/image-20210919161908762.png#crop=0&crop=0&crop=1&crop=1&id=MjjJW&originHeight=596&originWidth=802&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

![](https://picture-czy.oss-cn-beijing.aliyuncs.com/homeWinDir/image-20210919162023153.png#crop=0&crop=0&crop=1&crop=1&id=ZpKdk&originHeight=592&originWidth=796&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

**1.2.6 设置root密码**

![](https://picture-czy.oss-cn-beijing.aliyuncs.com/homeWinDir/image-20210919162130873.png#crop=0&crop=0&crop=1&crop=1&id=citTh&originHeight=586&originWidth=788&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

**1.2.7 点击开始安装，并等待15分钟左右**

![](https://picture-czy.oss-cn-beijing.aliyuncs.com/homeWinDir/image-20210919162213338.png#crop=0&crop=0&crop=1&crop=1&id=wMTqO&originHeight=562&originWidth=797&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

## 2. 在 Centos8 上安装KVM

### 2.1 先检查硬件是否支持虚拟化

```shell
grep -e 'vmx' /proc/cpuinfo       #Intel CPU
grep -e 'svm' /proc/cpuinfo       #AMD CPU
```

出现标有红色字样的字，则代表支持

![](https://picture-czy.oss-cn-beijing.aliyuncs.com/homeWinDir/image-20210919163644535.png#crop=0&crop=0&crop=1&crop=1&id=o6vFf&originHeight=390&originWidth=640&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

### 2.2 确认KVM模块是否已加载到内核中

```shell
lsmod | grep kvm
```

![](https://picture-czy.oss-cn-beijing.aliyuncs.com/homeWinDir/image-20210919163700576.png#crop=0&crop=0&crop=1&crop=1&id=iEvl1&originHeight=99&originWidth=627&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

### 2.3 安装cockpit Web控制台

> cockpit是**预先安装**的，并在新安装的**CentOS 8**和RHEL 8系统上启用。
**如果您没有安装它**，使用下面的dnf命令进行安装。其中cockpit-machines扩展是用来管理基于Libvirt的虚拟机的


使用阿里云镜像，速度更快

```shell
$ mv /etc/yum.repos.d/CentOS-Base.repo /etc/yum.repos.d/CentOS-Base.repo.backup
$ wget -O /etc/yum.repos.d/CentOS-Base.repo http://mirrors.aliyun.com/repo/Centos-8.repo

$ yum makecache
$ dnf install cockpit cockpit-machines
```

启动cockpit socket服务

```shell
$ systemctl start cockpit.socket
$ systemctl enable cockpit.socket
$ systemctl status cockpit.socket
```

配置防火墙

```shell
$ firewall-cmd --add-service=cockpit --permanent
$ firewall-cmd --reload
```

### 2.4 打开控制台

使用浏览器打开 `https://服务器IP:9090/`

用户名和密码和系统的一样

![](https://picture-czy.oss-cn-beijing.aliyuncs.com/homeWinDir/image-20210919165501904.png#crop=0&crop=0&crop=1&crop=1&id=l3n9P&originHeight=568&originWidth=711&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

![](https://picture-czy.oss-cn-beijing.aliyuncs.com/homeWinDir/image-20210919165522948.png#crop=0&crop=0&crop=1&crop=1&id=t55bR&originHeight=686&originWidth=1168&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

### 2.5 添加网桥

**2.5.1 点击左侧控制面板中 -网络-**

![](https://picture-czy.oss-cn-beijing.aliyuncs.com/homeWinDir/image-20210919171832156.png#crop=0&crop=0&crop=1&crop=1&id=cNEWh&originHeight=938&originWidth=1884&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

**2.5.2 点击 -添加网桥- 选择第一个网卡后点击应用**

![](https://picture-czy.oss-cn-beijing.aliyuncs.com/homeWinDir/image-20210919171950963.png#crop=0&crop=0&crop=1&crop=1&id=gHNG3&originHeight=924&originWidth=1873&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

### 2.6 添加虚拟机

**2.6.1 点击左侧控制面板中的 -虚拟机-**

![](https://picture-czy.oss-cn-beijing.aliyuncs.com/homeWinDir/image-20210919172152256.png#crop=0&crop=0&crop=1&crop=1&id=ncsE3&originHeight=935&originWidth=1879&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

**2.6.2 点击 -创建虚拟机-**

win7镜像我已经上传到`/opt/`目录下，也可以选择下载一个OS，但需要时间等待

![](https://picture-czy.oss-cn-beijing.aliyuncs.com/homeWinDir/image-20210919172621204.png#crop=0&crop=0&crop=1&crop=1&id=k6BaH&originHeight=668&originWidth=840&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

**2.6.3 点击创建好的虚拟机，编辑详细信息**

![](https://picture-czy.oss-cn-beijing.aliyuncs.com/homeWinDir/image-20210919172737235.png#crop=0&crop=0&crop=1&crop=1&id=RWzfJ&originHeight=465&originWidth=1680&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

这里我编辑了CPU的数量，自行发挥，之后点击安装

![](https://picture-czy.oss-cn-beijing.aliyuncs.com/homeWinDir/image-20210919173032404.png#crop=0&crop=0&crop=1&crop=1&id=hN3RO&originHeight=546&originWidth=1609&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

![](https://picture-czy.oss-cn-beijing.aliyuncs.com/homeWinDir/image-20210919173545440.png#crop=0&crop=0&crop=1&crop=1&id=qK6dd&originHeight=823&originWidth=1627&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

### 2.7 开启win7远程桌面

![](https://picture-czy.oss-cn-beijing.aliyuncs.com/homeWinDir/image-20210919174355047.png#crop=0&crop=0&crop=1&crop=1&id=YeHqV&originHeight=597&originWidth=795&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

![](https://picture-czy.oss-cn-beijing.aliyuncs.com/homeWinDir/image-20210919174446179.png#crop=0&crop=0&crop=1&crop=1&id=IHQZa&originHeight=597&originWidth=798&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

![](https://picture-czy.oss-cn-beijing.aliyuncs.com/homeWinDir/image-20210919174617403.png#crop=0&crop=0&crop=1&crop=1&id=pAJbE&originHeight=441&originWidth=413&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

点击选择用户可以查看和添加远程连接的用户

![](https://picture-czy.oss-cn-beijing.aliyuncs.com/homeWinDir/image-20210919175055213.png#crop=0&crop=0&crop=1&crop=1&id=elozX&originHeight=441&originWidth=413&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)
