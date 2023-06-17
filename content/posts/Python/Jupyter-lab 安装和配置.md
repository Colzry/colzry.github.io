---
title: "Jupyter lab安装和配置"
description: "Jupyter lab安装和配置"
keywords: "Jupyter lab"

date: 2023-06-16T16:41:30+08:00
lastmod: 2023-06-16T16:41:30+08:00

categories:
  - Python
tags:
  - Python
  - Jupyter
---

![](https://pica.zhimg.com/v2-ff135f26c3a322b1f83371a59edc903a_1440w.jpg?source=172ae18b#crop=0&crop=0&crop=1&crop=1&id=y1mt1&originHeight=674&originWidth=1181&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

## 通过pip安装

> 若没有pip可以先安装


```shell
sudo apt-get install python3-pip
```

安装 jupyter-lab

```shell
pip install jupyterlab
```

安装中文环境包

```shell
pip install jupyterlab-language-pack-zh-CN
```

## 配置局域网可以访问

先查看配置文件的位置

```shell
jupyter-lab --generate-config
```

![](https://picture-czy.oss-cn-beijing.aliyuncs.com/img/image-20210929195109661.png#crop=0&crop=0&crop=1&crop=1&id=Or1et&originHeight=73&originWidth=675&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

编辑配置文件

```shell
sudo vim ~/.jupyter/jupyter_lab_config.py
```

找到下面的配置，取消注释并修改成下面的样子(可通过**\**进行查找)，保存后退出

> **注意：**取消注释也要把注释后面的空格取消，不然启动时会报错


```shell
c.ServerApp.ip = '*'

# 下面的设置为可选的，不想设置的可以跳过(要通过root用户进行设置/普通用户和root用户的配置目录不一样)
# 设置默认打开的目录
c.ServerApp.root_dir = '/home/debian/' 
# 允许root用户运行登录
c.ServerApp.allow_root = True
# 允许远程访问
c.ServerApp.allow_remote_access = True
```

![](https://picture-czy.oss-cn-beijing.aliyuncs.com/img/image-20210929195721475.png#crop=0&crop=0&crop=1&crop=1&id=i9ejy&originHeight=92&originWidth=689&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

## 设置jupyter-lab网页的密码

```shell
jupyter-lab password
```

密码要输入两次

![](https://picture-czy.oss-cn-beijing.aliyuncs.com/img/image-20210929200012442.png#crop=0&crop=0&crop=1&crop=1&id=mHihJ&originHeight=148&originWidth=672&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

## 设置中文

启动jupyter-lab

```shell
jupyter lab
```

![](https://picture-czy.oss-cn-beijing.aliyuncs.com/img/image-20210929200532872.png#crop=0&crop=0&crop=1&crop=1&id=tEAdG&originHeight=181&originWidth=684&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

在浏览器中输入 [http://主机ip:8888/lab](http://xn--ip-wz2cm89g:8888/lab) 访问

进入后输入刚才设置的密码，进入主页面后设置中文

![](https://picture-czy.oss-cn-beijing.aliyuncs.com/img/image-20210929200426466.png#crop=0&crop=0&crop=1&crop=1&id=yrPCZ&originHeight=916&originWidth=1920&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

## 插件安装

```shell
pip install ipympl
pip install jupyterlab_github
pip install nglview
```

自动补全插件安装

```shell
pip install "jupyterlab-kite>=2.0.2"
bash -c "$(wget -q -O - https://linux.kite.com/dls/linux/current)"
```

### 安装主题

```shell
pip install jupyterthemes  # 主题安装
jt -l  # 显示可用主题
jt -t chosen_theme  # 切换主题
```

查看主题，携带的主题有7个：

- onedork
- grade3
- oceans16
- chesterish
- monokai
- solarizedl
- solarizedd

### 设置开机自启动

> 以服务的形式，配置开机启动项


```shell
vim /etc/systemd/system/jupyter.service
```

添加如下代码：

```shell
[Unit]
Description=Jupyter Notebook
After=network.target
[Service]
Type=simple
ExecStart=/usr/local/bin/jupyter-lab --config=/root/.jupyter/jupyter_server_config.json --no-browser
User=root
Group=root
WorkingDirectory=/root/workDir
Restart=always
RestartSec=10
[Install]
WantedBy=multi-user.target
```

设置自启动

```shell
sudo systemctl enable jupyter
sudo systemctl start jupyter
```

## 安装Java内核

```shell
sudo apt install openjdk-8-jre-headless
sudo apt install openjdk-8-jdk-headless
```

查看Java版本

```shell
java -version
```

安装IJava

```shell
wget https://github.com/SpencerPark/IJava/releases/download/v1.3.0/ijava-1.3.0.zip

unzip ijava-1.3.0.zip

python3 install.py --sys-prefix
```

查看内核

```shell
jupyter kernelspec list
```

![](https://picture-czy.oss-cn-beijing.aliyuncs.com/img/image-20210930124937564.png#crop=0&crop=0&crop=1&crop=1&id=Y1OyN&originHeight=121&originWidth=684&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

对应的web页面也有了

![](https://picture-czy.oss-cn-beijing.aliyuncs.com/img/image-20210930125037252.png#crop=0&crop=0&crop=1&crop=1&id=SSSIg&originHeight=764&originWidth=1494&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)
