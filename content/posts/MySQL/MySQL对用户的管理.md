---
title: "MySQL对用户的管理"
description: "MySQL对用户的管理"
keywords: "MySQL"

date: 2023-06-16T16:41:30+08:00
lastmod: 2023-06-16T16:41:30+08:00

categories:
  - MySQL
tags:
  - MySQL
---

## 1. 查看用户
```
use mysql;
select * from user;
```
**示例**
```shell
kylin@kylin:~$ mysql -u root -p
Enter password: 
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 14
Server version: 5.7.33 MySQL Community Server (GPL)

Copyright (c) 2000, 2021, Oracle and/or its affiliates.

Oracle is a registered trademark of Oracle Corporation and/or its
affiliates. Other names may be trademarks of their respective
owners.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

mysql> show databases;
+--------------------+
| Database           |
+--------------------+
| information_schema |
| mysql              |
| performance_schema |
| sys                |
+--------------------+
4 rows in set (0.00 sec)

mysql> use mysql;
Reading table information for completion of table and column names
You can turn off this feature to get a quicker startup with -A

Database changed
mysql> select * from user;
```

![](https://picture-czy.oss-cn-beijing.aliyuncs.com/img/20210206142920.png#crop=0&crop=0&crop=1&crop=1&id=RLeTO&originHeight=115&originWidth=1053&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

## 2. 创建用户
```
create user 'user_name'@'host' identified by 'passwd';
```
**user_name** 表示要创建的用户名
**host** 表示新创建的用户允许从哪台机器登录，**'localhost'**表示本机登录，**'%'** 表示远程登录
**passwd** 表示新用户的密码

**示例**
```
// 用户名为 mysql 只允许从本地登录 密码为 mysql
create user 'mysql'@'localhost' identified by 'mysql';
// 用户名为 mysql 允许远程登录 密码为 mysql
create user 'mysql'@'%' identified by 'mysql';
```

## 3. 授权用户
```
grant privileges on database_name.table_name to 'user_name'@'host';
```

**privileges** 表示要授予的权力，有 **select，insert，delete，update **等，如果要授予全部权力，就填 **all**
**database_name.table_name** 表示用户的权限能在某库某表中使用，如果要作用于数据库中的所有表，则填 ***.***
**user_name** 表示授权的用户
**host** 可填 localhost 或 %

**示例**
```
// 给test用户授予employee库job表的插入和查询权限
grant inster,select on employess.job to 'test'@'%';
// 给mysql用户授予所有库所有表的所示权限
grant all on *.* to 'mysql'@'%';
```

提示：如果当前是普通用户在执行命令则不能给其他用户授权，要想授权，需要在后面加上 with grant option
```
grant all on *.* to 'mysql'@'%' with grant option;
```

## 4. 更改用户的登录方式和密码
> **将本地登录改为可远程登录**

```
use mysql;
// 其中user_name为要更改的用户名
update user set host='%' where user = 'user_name';
// 刷新权限
flush privileges;
```

> **更改密码**

```
// 法一
// user_name为要更改的用户名 new_passwd为要更改的密码
set password for user_name@localhost = password('new_passwd');
// 法二
// name为要更改的用户名 new_passwd为要更改的密码
set password=password('new_passwd');
grant all on *.* to 'user_name'@'%' identified by 'new_passwd';
flush privileges;
```

## 5. 撤销用户的权限
```
revoke privileges on database_name.table_name from 'user_name'@'host';
```

## 6. 删除用户
```
drop user 'usre_name'@'host';
```
