---
title: "Linux用户的管理"
description: "Linux用户的管理"
keywords: "用户管理"

date: 2023-06-16T16:41:30+08:00
lastmod: 2023-06-16T16:41:30+08:00

categories:
  - Linux
tags:
  - Linux
---

# Linux 用户管理

## 一.用户管理命令

通过系统中的命令对用户进行相应的操作。在讲解命令之前，我们需要了解，在linux操作系统中，以下的几个**概念**：

- UID：用户ID号，用户的唯一标识号，就相当于一个人的身份证号。
- 所属用户组：在linux操作系统中，一个用户**必须**有它的用户组，如果不给新用户指定用户组，那么该会默认创建一个与用户名相同的组。
- GID：用户组的ID号。
- 家目录:与Windows系统相同，可理解为一个用户的用户文件夹，所有用户的家目录**默认**被创建在 **/home** 目录下。相当于Windows操作系统中的 **C:/Users** 目录。

### 1.1 创建用户

通过 `useradd` 命令来创建新的用户。

**语法格式：** useradd [参数] <用户名>

**常用参数：**

| 参数 | 作用                      |
| ---- | ------------------------- |
| -u   | 指定用户UID               |
| -d   | 指定用户家目录位置        |
| -c   | 添加用户说明/备注         |
| -g   | 指定用户初始所属的用户组  |
| -G   | 指定用户所属附加组        |
| -s   | 指定用户登录的shell解释器 |

**操作演示：**

添加新用户 xiaobei

```linux
[root@localhost ~]# useradd xiaobei
```

添加新用户 test01 并且指定其家目录为 /test/test01

```linux
[root@localhost ~]# useradd -d /test/test01 test01
```

添加新用户 test01 并且指定ID为6666

```linux
[root@localhost ~]# useradd -u 6666 test01
```

添加新用户 test01 并且指定其所属组为root，并设置其登录shell为nologin

```linux
[root@localhost ~]# useradd -g root -s /sbin/nologin test01
```

### 1.2 设置密码

通过`passwd`命令来设置当前登录用户(自身)或者其他用户的密码。该命令如果不加用户名，即代表对当前登录的用户进行操作。不加参数，代表设置密码。这里我们需要知道，通常创建用户都是root来做的，所以设置密码也都是root用户身份来进行设置。如果是普通用户想设置root用户的密码，怎么办呢？不是不可以，只是该普通用户必须拥有sudo权限。本文我们只需要理解可以这么做就好。

**语法格式：** passwd [参数] [用户名]

**常用参数：**

| 参数 | 作用               |
| ---- | ------------------ |
| -d   | 删除密码           |
| -S   | 查询用户密码的状态 |
| -l   | 锁定用户密码       |
| -u   | 解锁用户密码       |

**操作演示：**

设置当前登录用户的密码：

```linux
[root@localhost ~]# passwd
```

设置用户xiaobei的密码

```linux
[root@localhost ~]# passwd xiaobei
```

清除用户 xiaobei 的密码

```linux
[root@localhost ~]# passwd -d xiaobei
```

### 1.3 查看当前登录用户

使用命令`who` 与 `w` 命令可以查询当前系统上已登录用户的相关信息。

**语法格式：** who [参数]

**常用参数：**

| 参数 | 作用                 |
| ---- | -------------------- |
| -a   | 打印全面信息         |
| -b   | 打印系统最近引导时间 |
| -H   | 带有列名打印信息     |
| -u   | 打印已登录用户列表   |

**操作演示：**
输出当前已登录的用户信息(带列名打印)

```linux
[root@localhost ~]# who -H
名称      线路    时间              备注
root     pts/0  2020-12-30 16:16 (192.168.3.8)
123
```

注释：线路列表中的 pts/0 ，pts代表远程终端登录，如果输出了 tty1，则tty代表本地终端登录。

**语法格式：** w [参数]

**常用参数：**

| 参数 | 作用           |
| ---- | -------------- |
| -h   | 不带列名输出   |
| -s   | 使用短格式输出 |

**操作演示：**
输出当前已登录用户信息(带列名输出)

```linux
[root@localhost ~]# w
USER     TTY      FROM              LOGIN@   IDLE   JCPU   PCPU WHAT
root     pts/0    192.168.3.8      16:16    0.00s  0.46s  0.01s w
123
```

注释：LOGIN@ 代表登录时间，IDLE表示空闲时间，JCPU是与该终端连接的进程占用的时间，PCPU 是用户当前进程所占用的时间。

### 1.4 修改用户属性

使用`usermod`命令，可以修改用户相关属性和信息。

**语法格式：** usermod [参数] <用户名>

**常用参数：**

| 参数 | 作用                 |
| ---- | -------------------- |
| -u   | 修改用户UID          |
| -c   | 修改用户的说明/备注  |
| -g   | 修改用户的所属用户组 |
| -G   | 修改用户的附加组     |
| -L   | 锁定用户密码         |
| -U   | 解锁用户密码         |
| -s   | 修改用户的登录shell  |

**操作演示：**

设置用户xiaobei的登录shell为nologin 使其无法登录

```linux
[root@localhost ~]# usermod -s /sbin/nologin xiaobei
```

### 1.5 用户密码有效性

使用`chage`命令修改用户和用户密码的有效期限，这个信息由系统用于确定用户何时必须更改其密码。

**语法格式：** chage [参数] [用户名]

**常用参数：**

| 参数 | 作用                                   |
| ---- | -------------------------------------- |
| -M   | 密码保持有效的最大天数                 |
| -W   | 用户密码到期前，提前收到警告信息的天数 |
| -E   | 帐号到期的日期，会禁止此帐号           |
| -d   | 上一次更改的日期                       |
| -l   | 显示用户的密码相关信息                 |

**操作演示：**

使用户 xiaobei 的密码有效期最大为30天。

```linux
[root@localhost ~]# chage -M 30 xiaobei
```

### 1.6 删除用户

使用命令`userdel`删除用户。**在删除之前确定用户没有登录。**

**语法格式：** userdel [参数] [用户名]

**常用参数：**

| 参数 | 作用                 |
| ---- | -------------------- |
| -f   | 强制删除用户账号     |
| -r   | 同时删除用户的家目录 |

**操作演示：**
删除用户 test01 同时删除该用户的家目录

```linux
[root@localhost ~]# userdel -r test01
```

### 1.7 切换登录用户

使用 `su` 命令切换当前登录用户。root 用户切换普通用户时不需要输入密码，反之需要。

**语法格式：** su [用户名]

**常用参数：**

| 参数 | 作用                           |
| ---- | ------------------------------ |
| -c   | 仅执行一次命令，不切换用户身份 |

**操作演示：**

切换到用户 xiaobei

```linux
[root@localhost ~]# su xiaobei
```

切换到用户 root 同时一起切换环境变量。

```linux
[xiaobei@localhost ~]$ su - root
```

用root用户执行一条命令 useradd

```linux
[xiaobei@localhost ~]$ su - root -c "useradd test01"
```

### 1.8 踢出当前登录中的用户

我们想对某用户进行删除操作的时候，发现该用户正在被登录，这个时候我们就可以用命令 `pkill` ，使该用户被迫下线。知晓linux系统的同仁应该会了解，该命令其实是一个杀死进程的命令。其实在这里可以踢出登录的用户，也是可以理解的，因为一个用户在系统上登录，是会产生对应的进程的。因为在本贴我们主讲用户管理，所以在这里只介绍这一种用法。

**语法格式：** pkill -9 -t <终端号>

**操作演示：**

踢出在远程终端 终端号为 pts/1 登录的用户。

```linux
[root@localhost ~]# pkill -9 -t pts/1
```

**操作解释：**

这里的 -9 参数，是linux系统中的kill信号，在这里我们只需要知道这条命令可以立即踢出一个用户即可。 参数 -t 代表指定终端号。前面我们讲到过，可以通过 w 命令和 who 命令查询当前都有哪些用户登录，这里就是要配合这两个命令使用的，我们需要知道要踢出的用户所登录的终端号，一般踢出的都是远程用户，所以基本都是pts开头，这两个查询命令都可以查到用户登录的终端号，这个时候再使用该命令按终端号踢出用户，该用户就会立即下线。

### 1.9 查询用户登录信息

在Linux系统中可以查询到所有用户的登录信息，以及系统上近期的登录信息，通过两个命令可以查询：`lastlog` 和 `last`。

**语法格式：** lastlog [参数]

**常用参数：**

| 参数 | 作用                             |
| ---- | -------------------------------- |
| -b   | 显示指定天数前的登录信息         |
| -t   | 显示指定天数以来的登录信息       |
| -u   | 显示指定用户的最后一次的登录信息 |

**操作演示：**

查询root用户最后一次的登录信息

```linux
[root@localhost ~]# lastlog -u root
```

查询UID为 1000 的用户最近7天有没有登录过(有就会输出信息)。

```linux
[root@localhost ~]# lastlog -t 7 -u 1000
```

使用`last`命令查看的是系统上的用户登录记录及信息，以时间排序。

**语法格式：** last [参数]

**常用参数：**

| 参数 | 作用             |
| ---- | ---------------- |
| -R   | 简略输出(短格式) |
| -n   | 指定最近几条记录 |

**操作演示：**

查询系统上最近的十条登录信息。

```linux
[root@localhost ~]# last -n 10
```

使用`lastb`命令查看的是系统上的用户登录**失败的**记录及信息

**语法格式：** lastb [参数]

**常用参数：**

| 参数 | 作用                                             |
| ---- | ------------------------------------------------ |
| -a   | 把从何处登入系统的主机名称或ip地址显示在最后一行 |
| -n   | 设置列出名单的显示列数                           |
| -R   | 不显示登入系统的主机名称或IP地址                 |
| -x   | 显示系统关机，重新开机，以及执行等级的改变等信息 |

**操作演示：**
查询登录失败所有的信息

```linux
[root@localhost ~]#lastb
```

### 1.10 查询用户UID及GID信息

使用 `id` 命令可以查询用户的UID以及所属用户组的GID信息。

**语法格式：** id [参数] [用户名]

**常用参数：**

| 参数 | 作用                               |
| ---- | ---------------------------------- |
| -r   | 显示实际ID                         |
| -u   | 显示用户ID                         |
| -g   | 显示用户所属群组的ID               |
| -G   | 显示用户所属附加群组的ID           |
| -n   | 显示用户，所属群组或附加群组的名称 |

**操作演示：**

查询用户 xiaobei 的所有ID的信息

```linux
[root@localhost ~]# id xiaobei
```

### 1.11 退出登录

使用命令 `logout` 退出登录。远程终端以及`su`命令登录的用户也可用 `exit` 退出。

------

## 二.用户组管理命令

在前面我们提到过，在创建新用户时，会同时创建一个与该用户同名的用户组。这是因为Linux中的用户，必须有一个所属组，如果在创建用户时指定一个所属组，就不会创建与其同名的组了。下面我们只简单介绍几个组管理的命令。

### 2.1 添加用户组

使用命令`groupadd` 添加用户组。

**语法格式：** groupadd [参数] <组名>

**常用参数：**

| 参数 | 作用                   |
| ---- | ---------------------- |
| -g   | 创建的同时制定用户组ID |

**操作演示：**

添加用户组 userg 并指定id为6666

```linux
[root@localhost ~]# groupadd -g 6666 userg
```

### 2.2 修改组属性

使用命令`groupmod` 修改用户组的信息。

**语法格式：** groupmod [参数] <组名>

**常用参数：**

| 参数 | 作用         |
| ---- | ------------ |
| -n   | 修改组名     |
| -g   | 修改新的GUID |

**操作演示：**

修改用户组 userg 组名为testgroup

```linux
[root@localhost ~]# groupmod testgroup userg
```

### 2.3 设置用户组

使用命令`gpasswd` 来设置组和组内成员

**语法格式：** gpasswd <参数 > <组名>

**常用参数：**

| 参数 | 作用         |
| ---- | ------------ |
| -a   | 添加用户到组 |
| -d   | 从组删除用户 |

**操作演示：**

把用户 xiaobei 添加到 group01 用户组

```linux
[root@localhost ~]# gpasswd -a xiaobei group01
```

### 2.4 删除用户组

使用命令`groupdel` 来设置组和组内成员。如果组内有初始用户，则不能删除，如果组内有附加用户，也可以删除。

**语法格式：** groupdel [参数] <组名>

**操作演示：**

删除用户组 test01

```linux
[root@localhost ~]# groupdel test01
```

------

## 三.用户管理相关的配置文件

以上所有的用户管理、组管理命令，包括用户的添加、删除，密码的修改，有效期设置等命令的操作，均会保存在配置文件中，也就是命令的操作也就是修改配置文件。换一种说法，例如我们要修改某用户的密码有效期，除了可以使用命令，还可以直接修改Linux中记录用户信息的配置文件。都可以达到同样的效果。

### 3.1 用户信息配置文件

通过命令进行创建、修改用户以及相关操作，都是对配置文件`/etc/passwd`的修改。比如修改用户家目录，可以用命令 `usermod` 修改，也可以直接修改文件中的第六个字段。该文件除了记录普通用户的信息，也记录了系统用户的信息。**切记，系统用户的信息不要轻易更改，否则进行某相关操作时，会导致系统错误。**
通过命令 `less /etc/passwd` 或者 `vim /etc/passwd` 可以查看以及修改该配置文件。

**文件概览：**
![在这里插入图片描述](https://img-blog.csdnimg.cn/20201230180016251.PNG?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MzU3NTkwOA==,size_16,color_FFFFFF,t_70)
**字段对应信息：**

1.用户名称：2.密码标志：3.UID：4.GID ：5.用户说明/备注：6.家目录：7.登录的shell

**字段解释：**

密码标志 x 代表该用户有密码。
UID中 0 代表超级用户，RedHat、CentOS系列的发行版中，1-499为系统用户(伪用户),通过配置文件可以看到它们的shell解释器都为nologin也就是不登录。500-65535为普通用户可用的UID。
用户的附加组可以有多个。

### 3.2 用户密码信息配置文件

在Linux系统中`/etc/shadow`文件存放用户密码信息，又称为“影子文件”。由于存放了密码信息，为了保证安全性，该文件只有root用户可以读取。

**文件概览：**
![在这里插入图片描述](https://img-blog.csdnimg.cn/20201230181357303.PNG?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MzU3NTkwOA==,size_16,color_FFFFFF,t_70)
**字段对应信息：**

1.用户名称：2.加密密码：3.密码最后一次修改日期：4.两次密码的修改间隔：5.密码有效期：6.有效期到期前的警告天数：7.宽限天数：8.账号失效时间

**字段解释：**

加密密码采用了SHA512散列加密算法。如果该字段为 !! 或 * 就代表该用户不能登录。
该文件所有的**日期格式**均采用时间戳。
两次密码的修改间隔为天数，10 就代表修改过一次密码后十天内不能再次修改。

> unix时间戳是从1970年1月1日（UTC/GMT的午夜）开始所经过的秒数，不考虑闰秒。
> Unix时间戳（英文为Unix epoch, Unix time, POSIX time 或 Unix timestamp）
> 是从1970年1月1日（UTC/GMT的午夜）开始所经过的秒数，不考虑闰秒。
> UNIX时间戳的0按照ISO 8601规范为 ：1970-01-01T00:00:00Z.
> 一个小时表示为UNIX时间戳格式为：3600秒；一天表示为UNIX时间戳为86400秒，闰秒不计算。
> 在大多数的UNIX系统中UNIX时间戳存储为32位，这样会引发2038年问题或Y2038。
> -------- 百度百科

第七字段宽限天数，代表用户封禁前的缓冲天数，如果该值为3，就代表有效期过三天后再进行用户封禁。

### 3.3 创建用户默认配置信息文件

在创建用户时，我们可以手动指定家目录。如果不指定，就会默认把家目录放到`/home`目录下。如果我想让它默认创建到其他位置呢？我们就可以通过修改配置文件`/etc/default/useradd`文件来实现。

**文件概览：**
![在这里插入图片描述](https://img-blog.csdnimg.cn/20201230183625482.PNG?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MzU3NTkwOA==,size_16,color_FFFFFF,t_70)

**字段解释：**

HOME字段控制创建用户时默认的家目录位置
INACTIVE为密码过期的宽限天数
SHELL创建用户默认的登录shell
SKEL为家目录的模板目录
CREATE_MAIL_SPOOL是否建立邮箱
GROUP用户的默认组
EXPIRE密码失效时间

### 3.4 用户限制设定配置文件

在配置文件`/etc/login.defs`中，记录了用户限制设定。该文件的作用是为了对用户更为规范的管理，例如用户默认的密码有效期，就可以通过该文件进行修改，就是前面有讲到的`chage`命令相关的，不过该文件修改的是用户创建时的默认项，相当于一个模板文件。注意，**该文件设置对用户root不生效。**

**文件概览：**
![在这里插入图片描述](https://img-blog.csdnimg.cn/202012301847347.PNG?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MzU3NTkwOA==,size_16,color_FFFFFF,t_70)
**部分字段解释：**
PASS_MAX_DAYS 默认密码有效期
UID_MIN UID的最小值
PASS_MIN_DAYS 两次密码修改的间隔。

------

## 四.组管理相关的配置文件

与用户管理相同，除了命令，也可以通过直接修改配置文件来达到对组的配置。

### 4.1 组信息配置文件

用户组的配置文件位置为`/etc/group`。

**文件概览：**
![在这里插入图片描述](https://img-blog.csdnimg.cn/20201230182548752.PNG?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MzU3NTkwOA==,size_16,color_FFFFFF,t_70)

**字段对应信息：**

1.组名：2.组密码标志：3.GID：4.组中附加用户

### 4.2 组密码信息配置文件

组密码信息文件`/etc/gshadow`中存放着用户组的密码以及管理员用户名。

**文件概览：**
![在这里插入图片描述](https://img-blog.csdnimg.cn/20201230183043531.PNG?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MzU3NTkwOA==,size_16,color_FFFFFF,t_70)

**字段对应信息：**

1.组名：2.组密码：3.组管理员用户名：4.组中附加用户


## 五. 快速创建示例
```bash
#查看用户信息
less /etc/passwd
#查看组信息
less /etc/group

#创建用户组
groupadd colzry
#创建用户家目录
mkdir -p /home/colzry
#创建用户 
useradd -d /home/colzry -s /bin/bash colzry
#设置用户密码
passwd colzry
#用户家目录赋权755
chmod -R 755 /home/colzry
#切换用户
su colzry
#修改文件所属用户:组
chown -R colzry:colzry filename
```