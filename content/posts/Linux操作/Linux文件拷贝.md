---
title: "Linux文件拷贝"
description: "Linux文件拷贝"
keywords: "拷贝"

date: 2023-06-16T16:41:30+08:00
lastmod: 2023-06-16T16:41:30+08:00

categories:
  - Linux
tags:
  - Linux
---

## scp

### 推送

```shell
scp -r 文件名 目标用户@主机名(host):目标目录
```

```shell
[colzry@hadoop102 ~]$ cd /opt/module/
[colzry@hadoop102 module]$ pwd
/opt/module
[colzry@hadoop102 module]$ ll
总用量 0
drwxr-xr-x. 9 colzry root 149 9月  12 2019 hadoop-3.1.3
drwxr-xr-x. 7 colzry root 245 4月   2 2019 jdk1.8.0_212
[colzry@hadoop102 module]$ scp -r jdk1.8.0_212/ colzry@hadoop103:/opt/module/
```

### 拉取

```shell
scp -r 目标用户@主机名(host):目标目录 放置目录
```

```shell
[colzry@hadoop103 module]$ scp -r colzry@hadoop102:/opt/module/hadoop-3.1.3 ./
```

### 中转

```shell
scp -r 目标用户@主机名(host):目标目录 目标用户@主机名(host):目标目录
```

```shell
[colzry@hadoop103 module]$ scp -r colzry@hadoop102:/opt/module/* colzry@hadoop104:/opt/module/
```

## rsync(推荐使用)

```shell
rsync -av 文件名 目标用户@主机名(host):目标目录
rsync -av 目标用户@主机名(host):目标目录 目标目录
rsync -av 目标用户@主机名(host):目标目录 目标用户@主机名(host):目标目录
```

### 集群分发脚本

```shell
#!/bin/bash
#1. 判断参数个数
if [ $# -lt 1 ]
then
	 echo Not Enough Arguement!
	 exit;
fi
#2. 遍历集群所有机器
for host in hadoop102 hadoop103 hadoop104
do
	 echo ==================== $host ====================
	 #3. 遍历所有目录，挨个发送
	 for file in $@
	 do
		 #4. 判断文件是否存在
		 if [ -e $file ]
		 then
			 #5. 获取父目录
			 pdir=$(cd -P $(dirname $file); pwd)
			 #6. 获取当前文件的名称
			 fname=$(basename $file)
			 ssh $host "mkdir -p $pdir"
			 rsync -av $pdir/$fname $host:$pdir
		 else
			 echo $file does not exists!
		 fi
	 done
done
```

### 使用方法

```shell
cd ~
mkdir bin
vim xsync // 填入上面脚本内容
chmod 777 xsync
xsync /etc/profile.d/my_env.sh
```

## 集群状态查看脚本
```bash
#!/bin/bash
# 获取控制台指令
# 判断指令是否为空
if [ $# -lt 1 ]
then
	echo "command can not be null !"
	exit
fi

# 获取当前登录用户
user=`whoami`
source /etc/profile
# 在从机执行指令,这里需要根据你具体的集群情况配置，host与具体主机名一致
for host in hadoop101  hadoop102 hadoop103
do
	echo ================ $host=================
	ssh $user@$host $@
done
echo ===========================================
```

### 使用方法
```bash
cd ~
mkdir bin
vim xcall // 填入上面脚本内容
chmod 777 xcall
xcall jps
```