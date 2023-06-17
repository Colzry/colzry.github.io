---
title: "Debian安装MySQL"
description: "Debian安装MySQL"
keywords: "MySQL"

date: 2023-06-16T16:41:30+08:00
lastmod: 2023-06-16T16:41:30+08:00

categories:
  - MySQL
tags:
  - MySQL
---

### 1. 将 MySQL APT 存储库添加到系统
```bash
wget http://repo.mysql.com/mysql-apt-config_0.8.13-1_all.deb 
sudo dpkg -i mysql-apt-config_0.8.13-1_all.deb 
# 或者
apt-get install ./mysql-apt-config_0.8.13-1_all.deb
```
![image.png](https://cdn.nlark.com/yuque/0/2022/png/22223333/1657724175212-ef516da0-0091-4a2b-ba29-613b12e7b084.png#clientId=u33237a36-5953-4&crop=0&crop=0&crop=1&crop=1&from=paste&height=515&id=u8b53c603&margin=%5Bobject%20Object%5D&name=image.png&originHeight=515&originWidth=850&originalType=binary&ratio=1&rotation=0&showTitle=false&size=57261&status=done&style=none&taskId=u320aed19-e39e-464b-b0c6-2e398b84bb6&title=&width=850)
**选择第一个，按 Tabel 键到确定进行版本的选择，这里选择了5.7，之后来到OK选择，按Table键确定后退出**
### 2. 更新软件包，并安装MySql
```bash
sudo apt-get update 
sudo apt install mysql-server 
```
### 3. 安装过程中会要求输入root密码
![image.png](https://cdn.nlark.com/yuque/0/2022/png/22223333/1657724264210-46690c7d-e4a3-4cb3-8fc4-338677892623.png#clientId=u33237a36-5953-4&crop=0&crop=0&crop=1&crop=1&from=paste&height=496&id=ud97b5f82&margin=%5Bobject%20Object%5D&name=image.png&originHeight=496&originWidth=857&originalType=binary&ratio=1&rotation=0&showTitle=false&size=43874&status=done&style=none&taskId=uad785181-f17b-4281-b8c2-6585024e024&title=&width=857)
### 4. 创建用户并授权
```bash
use mysql  create user 'colzry'@'%' identified by 'colzry_admin'; 
grant all on *.* to 'colzry'@'%'; 
flush privileges; 
```
### 5. 将本地登录改为可远程登录
```bash
use mysql; 
// 其中user_name为要更改的用户名 
update user set host='%' where user = 'user_name'; 
// 刷新权限 
flush privileges; 
```
**5.1还需修改配置文件**
```bash
vim /etc/mysql/mysql.conf.d/mysqld.cnf 
```
![image.png](https://cdn.nlark.com/yuque/0/2022/png/22223333/1657724386047-7f8ac211-a874-40ca-8fb1-9341dba0b455.png#clientId=u33237a36-5953-4&crop=0&crop=0&crop=1&crop=1&from=paste&height=239&id=u38f2ed95&margin=%5Bobject%20Object%5D&name=image.png&originHeight=239&originWidth=534&originalType=binary&ratio=1&rotation=0&showTitle=false&size=162703&status=done&style=none&taskId=uff506f62-adf6-47a3-97a9-327352a7ac8&title=&width=534)
### 6. 更改密码
```bash
// 法一 
// user_name为要更改的用户名 new_passwd为要更改的密码 
set password for user_name@localhost = password('new_passwd'); 

// 法二 
// name为要更改的用户名 new_passwd为要更改的密码 
set password=password('new_passwd'); 
grant all on *.* to 'user_name'@'%' identified by 'new_passwd'; 
flush privileges; 
```
### 7. 卸载
```bash
apt-get remove --purge 'mysql-.*' apt-get remove --purge 'mysql-server.*' 
```
