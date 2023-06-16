---
title: "树莓派使用Docker安装openwrt作为旁路由(网关服务器)"
date: 2021-10-02T10:10:26+08:00
categories:
 - Docker
tags:
  - Docker
  - 树莓派
---

![](https://picture-czy.oss-cn-beijing.aliyuncs.com/homeLinux/image-20210821194614451.png#crop=0&crop=0&crop=1&crop=1&id=XYpef&originHeight=876&originWidth=1913&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

> 推荐使用 `树莓派爱好基地的arm64无桌面增强版`镜像


仓库地址：[https://github.com/openfans-community-offical/Debian-Pi-Aarch64/blob/master/README_zh.md](https://github.com/openfans-community-offical/Debian-Pi-Aarch64/blob/master/README_zh.md)
仓库中有详细的说明文档和下载地址

## 开始安装openwrt容器

> `树莓派爱好基地的arm64无桌面增强版`自带docker，可以直接使用


### 1. 打开网卡混杂模式

```shell
sudo ip link set eth0 promisc on
```

### 2. 创建macvlan虚拟网络，同一网段下的主机才能访问容器

下面的网段(subnet)和网关(gateway)选项请结合实际自行更改

```shell
docker network create -d macvlan --subnet=192.168.0.0/24 --gateway=192.168.0.1 -o parent=eth0 macnet
```

此时，我们使用 `docker network ls`命令可以看到网络`macnet`已建立成功：

```shell
pi@raspbian:~$ docker network ls
NETWORK ID     NAME      DRIVER    SCOPE
7b8e38d3dd3c   bridge    bridge    local
f96e6360c248   host      host      local
7c7a5a51b268   macnet    macvlan   local
c8c6782b8e1e   none      null      local
```

### 3. 拉取openwrt镜像

```shell
docker pull registry.cn-shanghai.aliyuncs.com/suling/openwrt:rpi4
```

镜像拉取完成后，我们可以执行`docker images`命令查看现存镜像：

```shell
docker images
REPOSITORY                                         TAG       IMAGE ID       CREATED        SIZE
registry.cn-shanghai.aliyuncs.com/suling/openwrt   rpi4      c3ba4d17a20e   32 hours ago   455MB
```

### 4. 创建并启动容器

```shell
docker run --restart always --name openwrt -d --network macnet --privileged --ip 192.168.0.200 registry.cn-shanghai.aliyuncs.com/suling/openwrt:rpi4 /sbin/init
```

其中：

`--restart always`参数表示容器退出时始终重启，使服务尽量保持始终可用；

`--name openwrt`参数定义了容器的名称；

`-d`参数定义使容器运行在 Daemon 模式(后台运行)；

`--network macnet`参数定义将容器加入 `maxnet`网络；

`--privileged`参数定义容器运行在特权模式下；

`--ip 192.168.0.200`指定容器的ip

`registry.cn-shanghai.aliyuncs.com/suling/openwrt:latest`为 Docker 镜像名，因容器托管在阿里云 Docker 镜像仓库内，所以在镜像名中含有阿里云仓库信息；

`/sbin/init`定义容器启动后执行的命令。

启动容器后，我们可以使用 `docker ps -a`命令查看当前运行的容器：

```shell
 docker ps -a
CONTAINER ID   IMAGE                                                   COMMAND        CREATED        STATUS       PORTS     NAMES
5cd19f4cd735   registry.cn-shanghai.aliyuncs.com/suling/openwrt:rpi4   "/sbin/init"   20 hours ago   Up 7 hours             openwrt
```

### 5. 进入容器修改ip,网关和dns

```shell
docker exec -it openwrt ash
```

执行此命令后我们便进入 OpenWrt 的命令行界面，首先，我们需要编辑 OpenWrt 的网络配置文件：

```shell
vim /etc/config/network
```

我们需要更改 Lan 口设置：

```shell
config interface 'lan'
        option ifname 'eth0'
        option proto 'static'
        option netmask '255.255.255.0'
        option ip6assign '60'
        option ipaddr '192.168.0.200'
        option gateway '192.168.0.1'
        option dns '192.168.0.1'
```

### 6. 保存后重启网络

```shell
/etc/init.d/network restart
```

按下`Ctrl + D`可以退出openwrt的终端

### 7. 进入luci 控制面板

在浏览器中输入第 5 步`option ipaddr`项目中的 IP 进入 Luci 控制面板，若`option ipaddr`的参数为 `192.168.0.200`，则可以在浏览器输入 `http://192.168.0.200`进入控制面板。

用户名：`root`

密码：`password`

### 8. 配置防火墙

在网络-防火墙-自定义规则添加以下命令后重启防火墙

```shell
iptables -t nat -I POSTROUTING -o eth0 -j MASQUERADE
```

![](https://picture-czy.oss-cn-beijing.aliyuncs.com/homeLinux/image-20210821192955982.png#crop=0&crop=0&crop=1&crop=1&id=nUNmi&originHeight=646&originWidth=1916&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

### 9. 关闭dhcp服务

在网络-接口处删除多于的网络，只保留LAN口，点击LAN口的修改

![](https://picture-czy.oss-cn-beijing.aliyuncs.com/homeLinux/image-20210821193540758.png#crop=0&crop=0&crop=1&crop=1&id=lY5Yh&originHeight=663&originWidth=1919&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

来到最下面的基本设置，勾上忽略此接口，然后保存应用即可

![](https://picture-czy.oss-cn-beijing.aliyuncs.com/homeLinux/image-20210821193754276.png#crop=0&crop=0&crop=1&crop=1&id=DuhsP&originHeight=268&originWidth=1668&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

## 10. 将网关指向openwrt

来到路由器的后台管理，将路由器的网关指向openwrt的ip地址即可

![](https://picture-czy.oss-cn-beijing.aliyuncs.com/homeLinux/image-20210821194202478.png#crop=0&crop=0&crop=1&crop=1&id=BZ0JY&originHeight=507&originWidth=1098&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

> 参考： [https://mlapp.cn/376.html](https://mlapp.cn/376.html)

