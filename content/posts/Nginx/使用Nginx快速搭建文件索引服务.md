---
title: "使用Nginx快速搭建文件索引服务"
description: "使用Nginx快速搭建文件索引服务"
keywords: "Nginx"

date: 2023-06-16T16:41:30+08:00
lastmod: 2023-06-16T16:41:30+08:00

categories:
  - Nginx
tags:
  - Nginx
---

## 使用宝塔面板

**在宝塔面板找到Nginx，点击设置，打开配置修改**

![](https://picture-czy.oss-cn-beijing.aliyuncs.com/img/image-20211003154737017.png#crop=0&crop=0&crop=1&crop=1&id=xj1p5&originHeight=915&originWidth=1876&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

**在http的花括号中添加以下内容**

```shell
server {
  listen 80; # 监听80端口
  server_name localhost; # 自己PC的ip或者服务器的域名
  charset utf-8; # 避免中文乱码
  root /opt/data; # 存放文件的目录      
  location / { 
    autoindex on; # 索引 
    autoindex_exact_size on; # 显示文件大小
    autoindex_localtime on; # 显示文件时间 
  }
}
```

**修改完成后，点击最上面的服务，选择重载配置**

![](https://picture-czy.oss-cn-beijing.aliyuncs.com/img/image-20211003155126989.png#crop=0&crop=0&crop=1&crop=1&id=W27wI&originHeight=755&originWidth=811&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

**打开主机ip的80端口就能看到文件索引了**

![](https://picture-czy.oss-cn-beijing.aliyuncs.com/img/image-20211003155242987.png#crop=0&crop=0&crop=1&crop=1&id=RU1zi&originHeight=243&originWidth=998&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

## 不使用宝塔面板

**1.安装nginx**

```shell
sudo apt-get install nginx
```

**2.创建conf文件**

```shell
sudo vim /etc/nginx/conf.d/file_server.conf
```

**3.修改conf文件如下：**

```shell
server {
  listen 80; # 监听80端口
  server_name localhost; # 自己PC的ip或者服务器的域名
  charset utf-8; # 避免中文乱码
  root /opt/data; # 存放文件的目录      
  location / { 
    autoindex on; # 索引 
    autoindex_exact_size on; # 显示文件大小
    autoindex_localtime on; # 显示文件时间 
  }
}
```

**3.使配置生效**

```shell
sudo rm /etc/nginx/sites-enabled/default 
sudo service nginx reload
```

**4.重启命令**

```shell
 sudo /etc/init.d/nginx start|stop|reload|
 sudo service nginx start|stop|reload|
```

**5.访问**
浏览器里直接输入 主机IP:80
