---
title: "MySQL数据库的备份和恢复"
description: "MySQL数据库的备份和恢复"
keywords: "MySQL"

date: 2023-06-16T16:41:30+08:00
lastmod: 2023-06-16T16:41:30+08:00

categories:
  - MySQL
tags:
  - MySQL
---

## 1. 备份
### 1.1创建备份脚本
在`/usr/local/sbin/`下新建`mysql_backup.sh`
```powershell
sudo vim /usr/local/sbin/mysql_backup.sh
```
填入下面的内容
```powershell
#!/bin/bash 
bakdir=/data/back # 定义备份文件存放目录
d=`date +%F` # 以日期(Y-M-D)为命名格式
user=colzry # 数据库用户名 (!自行更改)
passwd=passwd # 密码 (!自行更改)

# 备份两个数据库 typecho lsky (!自行更改)
# 若要备份所以的数据库,则下面的整条for语句改为下面注释的内容
# mysqldump -u$user -p$passwd -A > $bakdir/$db\_$d.sql
for db in typecho lsky
    do
        mysqldump -u$user -p$passwd $db > $bakdir/$db\_$d.sql
    done
    
cd $bakdir
# 对备份文件进行压缩
gzip *_$d.sql
# 对备份时间超过一个月的文件进行删除
find ./ -name "*.gz" -mtime +30 | xargs rm -f
```

### 1.2 创建定时任务
**用法**
格式为 : 
```powershell
 * * * * *
分 时 天 月 周
```
示例:
```powershell
*/1 * * * * Command 每f分钟执行
0 */1 * * * Command 每小时执行
5 * * * * Command 每小时的第5分钟执行一次命令
30 18 * * * Command 指定每天下午的 6:30 执行一次命令
30 7 8 * * Command 指定每月8号的7：30分执行一次命令
30 5 8 6 * Command 指定每年的6月8日5：30执行一次命令
30 6 * * 0 Command 指定每星期日的6:30执行一次命令
```
```powershell
crontab -e  编辑某个用户的cron服务
crontab -l 列出某个用户cron服务的详细内容
```
### 1.3 开始创建定时任务
```powershell
sudo crontab -e
```
```powershell
# 每天凌晨3:30执行数据库的备份
30 3 * * * /bin/bash /usr/local/sbin/mysql_back.sh
```

## 2. 恢复
来到备份的目录
```powershell
cd /data/back
```
```powershell
# lsky 为要恢复的数据库，回车后输入该用户的数据库密码即可
gunzip < lsky_2021-08-19.sql.gz | mysql -u colzry -p lsky
```



## 3. 对Docker里的数据库备份
### 3.1 本地版
```bash
#!/bin/bash 
backdir=/data/back # 定义备份文件存放目录
d=`date +%F` # 以日期(Y-M-D)为命名格式
user=colzry # 数据库用户名 (!自行更改)
passwd=colzry_admin # 密码 (!自行更改)

# 检查本地文件夹是否存在
if [ ! -d $backdir ];then
mkdir -p $backdir
fi

docker exec mysql sh -c 'exec mysqldump -u'${user}' -p'${passwd}' -A' > $backdir/$d.sql

cd $backdir
# 对备份文件进行压缩
gzip $d.sql
# 对备份时间超过10天的文件进行删除
find ./ -name "*.gz" -mtime +10 | xargs rm -f
```

### 3.2 远程版

```bash
#!/bin/bash 
backdir=/data/back # 定义备份文件存放目录
d=`date +%F` # 以日期(Y-M-D)为命名格式
user=colzry # 数据库用户名 (!自行更改)
passwd=colzry_admin # 密码 (!自行更改)
host=root@47.120.35.102 # 服务器用户名和地址，使用前先上传ssh公钥

# 检查本地文件夹是否存在
if [ ! -d $backdir ];then
mkdir -p $backdir
fi

docker exec mysql sh -c 'exec mysqldump -u'${user}' -p'${passwd}' -A' > $backdir/$d.sql

cd $backdir
# 对备份文件进行压缩
gzip $d.sql
# 对备份时间超过10天的文件进行删除
find ./ -name "*.gz" -mtime +10 | xargs rm -f

# 上传文件到服务器
rsync -av $d.sql.gz $host:$backdir
```