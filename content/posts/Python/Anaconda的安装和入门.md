---
title: "Anaconda的安装和入门"
description: "Anaconda的安装和入门"
keywords: "Anaconda"

date: 2023-06-16T16:41:30+08:00
lastmod: 2023-06-16T16:41:30+08:00

categories:
  - Python
tags:
  - Python
  - Anaconda
---

## 1.0安装

### 1.1下载

Anaconda 下载地址：

[https://www.anaconda.com/products/individual#download-section](https://www.anaconda.com/products/individual#download-section)

安装方法：无脑下一步就行(**注意：**若想更改安装目录可自行设置)

### 1.2配置环境变量

1.2.1 将**Anaconda 的安装目录**和**安装目录下的Scripts目录**这两个地址添加到 Path 中即可

![](https://picture-czy.oss-cn-beijing.aliyuncs.com/img/image-20211020164042238.png#crop=0&crop=0&crop=1&crop=1&id=I3mjx&originHeight=133&originWidth=666&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

1.2.2 检查是否配置成功

```shell
conda -V
```

![](https://picture-czy.oss-cn-beijing.aliyuncs.com/img/image-20211020164433714.png#crop=0&crop=0&crop=1&crop=1&id=Td30o&originHeight=303&originWidth=1119&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

## 2.0换源

### 2.1打开终端依次输入下面的内容

```shell
conda config --add channels http://mirrors.ustc.edu.cn/anaconda/pkgs/free/
conda config --add channels http://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud/conda-forge/
conda config --add channels http://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/free/
conda config --set show_channel_urls yes
```

### 2.2删除默认源

在用户根目录（C:\Users\用户名）下找到.condarc文件，打开并并编辑，删除其中的default配置行

![](https://picture-czy.oss-cn-beijing.aliyuncs.com/img/image-20211020164944636.png#crop=0&crop=0&crop=1&crop=1&id=rdLEl&originHeight=618&originWidth=1011&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

### 2.3 初始化powershell

如不初始化powershell，则在powershell中无法激活conda环境
以管理员的方式进入powershell，输入下面的内容

```bash
conda init powershell
```

## 3.0 基本使用

### 3.1 更新conda版本

```shell
conda update conda
```

### 3.2 查看conda帮助信息

```shell
conda -h / conda --help
```

### 3.3 创建新环境

默认情况下，新创建的环境将会被保存在**anconda**安装目录下的**envs**中

```shell
conda create --name <env_name> <package_names>
```

<env_name>：新建环境名

<package_names>：新创建的环境中创建的包，多个包使用空格分隔

### 3.4 列出已有的环境

```shell
conda info --env / conda env list
```

### 3.5 切换(激活)/取消(不激活)环境

**3.5.1 切换环境**

```shell
conda activate <env_name>
```

**3.5.2 取消环境**

```shell
conda deactivate <env_name>
```

### 3.6 删除环境

```shell
conda remove --name <env_name> --all
```

### 3.7 复制环境

```shell
conda create --name <new_env_name> --clone <copy_env_name>
```

### 3.8 导入和导出环境

**3.8.1 导出当前环境的包信息，将包信息存入yaml文件中**

导出的文件在当前命令执行的所在目录

```shell
conda env export > env.yaml
```

**3.8.2 导入yaml文件的环境信息**

```shell
conda env create -f env.yaml
```

### 3.9 包的管理

**3.9.1 列出当前环境的包**

```shell
conda list
```

**3.9.2 查找可安装的包**

```shell
conda search <package_name>
```

**3.9.3 安装包**

当前环境安装

```shell
conda install <package_names>
```

指定环境安装

```shell
conda install --name <env_name> <package_names>
```

pip安装（只能在当前环境安装）

```shell
pip install <package_names>
```

**3.9.4 卸载包**

卸载当前环境的包

```shell
conda remove <package_names>
```

卸载指定环境的包

```shell
conda remove --name <env_name> <packages_names>
```

**3.9.5 更新包**

更新指定包

```shell
conda update <package_name>
```

更新所有包

```shell
conda update --all
```
