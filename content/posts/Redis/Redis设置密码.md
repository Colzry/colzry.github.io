---
title: "Redis设置密码"
description: "Redis设置密码"
keywords: "Redis"

date: 2023-06-16T16:41:30+08:00
lastmod: 2023-06-16T16:41:30+08:00

categories:
  - Redis
tags:
  - Redis
---

### 方法一：通过配置文件（/etc/redis.conf）进行设置

这种方法在设置密码后需要重启redis生效。首先找到redis的配置文件—redis.conf文件，然后修改里面的requirepass（requirepass 是配置redis访问密码的参数），这个本来是注释起来了的，将注释去掉，并将后面对应的字段设置成自己想要的密码，保存退出。重启redis服务，即可。

![](https://picture-czy.oss-cn-beijing.aliyuncs.com/img/202206102248389.png#crop=0&crop=0&crop=1&crop=1&id=lhAbH&originHeight=190&originWidth=553&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

我这里是设置密码成了123

### 方法二：通过命令设置密码

这种方法相对简单，不需要重启redis服务。连接redis之后，通过命令设置，如下：

```bash
config set requirepass 123456
```

如此，便将密码设置成了123456

设置之后，可通过以下指令查看密码

```bash
config get requirepass
```

![](https://picture-czy.oss-cn-beijing.aliyuncs.com/img/202206102248700.png#crop=0&crop=0&crop=1&crop=1&id=Js1cd&originHeight=163&originWidth=719&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

密码设置之后，当你退出再次连上redis的时候，就需要输入密码了，不然是无法操作的。这里有两种方式输入密码，一是连接的时候直接输入密码，而是连接上之后再输入密码，分别如下所示：

![](https://picture-czy.oss-cn-beijing.aliyuncs.com/img/202206102248699.png#crop=0&crop=0&crop=1&crop=1&id=aHNv1&originHeight=217&originWidth=913&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

注：通过命令行修改了密码之后，配置文件（/etc/redis.conf）的requirepass字段后面的密码是不会随之修改的。
