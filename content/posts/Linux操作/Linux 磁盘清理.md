---
title: "Linux磁盘清理"
description: "Linux磁盘清理"
keywords: "磁盘清理"

date: 2023-06-16T16:41:30+08:00
lastmod: 2023-06-16T16:41:30+08:00

categories:
  - Linux
tags:
  - Linux
---

## 1. 查看磁盘信息
```bash
df -lh
```


我们可以看见Filesystem下的挂载点 /dev/vda1 下的8.4G容量已经耗尽。接下来就是删除占用磁盘空间大，但又无用的文件。

![image-20230107212434401](https://picture-czy.oss-cn-beijing.aliyuncs.com/img/image-20230107212434401.png)

## 2. 定位最大文件目录

```bash
cd /  
#寻找当前目录，哪个文件夹占用空间最大  
du -h --max-depth=1
```

![image-20230107212656434](https://picture-czy.oss-cn-beijing.aliyuncs.com/img/image-20230107212656434.png)

可以看到 /var 此路径占用较大磁盘空间，占用了6G。

重复上面的步骤，定位到最后的目录

![image-20230107212912664](https://picture-czy.oss-cn-beijing.aliyuncs.com/img/image-20230107212912664.png)

可以看到 /log 此路径占用较大磁盘空间，占用了5.8G。

## 3. 定位最大文件

进入/log目录，查看里面的文件情况

![image-20230107213223122](https://picture-czy.oss-cn-beijing.aliyuncs.com/img/image-20230107213223122.png)

可以看到里面有几个G的系统文件，查看之后再确认是否删除

## 4. 删除文件

```bash
rm -f messages*
```