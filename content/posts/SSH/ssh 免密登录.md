---
title: "SSH免密登录"
description: "SSH免密登录"
keywords: "免密"

date: 2023-06-16T16:41:30+08:00
lastmod: 2023-06-16T16:41:30+08:00

categories:
  - SSH
tags:
  - SSH
---

## 安装

```shell
# 安装服务端/客户端(Ubuntu)
$ sudo apt install openssh-server/openssh-client

# 查看ssh服务是否开启
$ netstat -tlp | grep ssh

# 启动/停止/重启 ssh服务
$ sudo /etc/init.d/ssh start/stop/restart
```

- ssh服务端配置文件默认为`/etc/ssh/sshd_config`。可以按需修改默认22端口等配置。

## 普通登录方式

```shell
# 命令格式
$ ssh [-options] [user@hostname]
```
| options | 含义 |
| --- | --- |
| `-p` | 指定ssh端口号,默认端口为`22` |
| `-i` | 使用指定私钥文件连接服务器([免密登录](https://ccstudio.com.cn/linux/part1/remote.html#2-%E5%85%8D%E5%AF%86%E7%99%BB%E5%BD%95)
) |


- `user`远程服务器登录的用户名，默认为当前用户
- `hostname`远程服务器地址。可以是IP/域名/别名
- `exit`或`logout`命令均可退出当前登录

```shell
# 以hadoop用户登录192.168.1.196的到ssh服务器
$ ssh  hadoop@192.168.1.196

# 以hadoop户登录到192.168.1.198的ssh服务器，使用8022端口
$ ssh hadoop@192.168.1.198 -p 8022
```

## ssh高级配置

ssh配置信息都保存在`~/.ssh` 中

| 配置文件 | 作用 |
| --- | --- |
| known_hosts | 作为客户端。记录曾连接服务器授权。ssh第一次连接一台服务器会有一个授权提示，确认授权后会记录在此文件中，下次连接记录中的服务器时则不再需要进行授权确认提示 |
| authorized_keys | 作为服务端。客户端的免密连接公钥文件 |
| config | 作为客户端。记录连接服务器配置的别名 |


### 服务器别名

远程管理命令(如ssh,scp等)连接一台服务器时一般都需要提供 服务器地址、端口、用户名 ，每次输入比较繁琐，我们可以把经常使用的服务器连接参数打包记录到配置文件中并为其设置一个简单易记的别名。这样我们就可以通过别名方便的访问服务器，而不需要提供地址、端口、用户名等信息了。

配置方法如下：

-  创建或打开 `~/.ssh/config`，在文件追加服务器配置信息 
-  一台服务器配置格式如下 
```json
Host 【别名】
  HostName 192.168.1.196
  User hadoop
  Port 22
```


## 免密登录

-  远程管理命令(如ssh,scp等)每次都需要提供用户密码保证安全。除此之外，我们也可配置使指定加密算法验证密钥文件的方式，避免每次输入密码 
-  配置免密登录后，ssh连接和scp等远程管理命令都不需要再输密码 
-  生成密钥时若指定了文件名，连接服务器时需要通过`-i`指定要验证的密钥文件,形如：`ssh -i file user@host`。默认文件名则可省略 
-  默认配置只需以下两步： 
```shell
# 客户端生成密钥对
$ ssh-keygen

# 上传公钥到服务器
$ ssh-copy-id user@host  # 文件会自动上传为服务器特定文件 ～/.ssh/authorized_keys

#  客户端登录
$ ssh -i ~/.ssh/id_rsa user@host
```

`如果配置好config文件，可使用别名登录`  
使用IdentityFile方式进行登录，每次ssh登录都要通过`-i`选项指定私钥路径比较繁琐，我们可以将密钥文件添加到ssh客户端config中以简化连接命令。   
### 简化登录
