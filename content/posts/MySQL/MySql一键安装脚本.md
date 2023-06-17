---
title: "MySQL一键安装脚本"
description: "MySQL一键安装脚本"
keywords: "MySQL"

date: 2023-06-16T16:41:30+08:00
lastmod: 2023-06-16T16:41:30+08:00

categories:
  - MySQL
tags:
  - MySQL
---

## 1.  安装脚本
```bash
wget https://files.catbox.moe/lwh5vs.sh -O /root/install-mysql.sh && chmod +x /root/install-mysql.sh && /root/install-mysql.sh
```
## 2. 默认密码
> **user: root  password: admin**

## 3. 其他方式
下载不了的话，运行下面脚本
### 3.1 使用yum的(建议使用)
```bash
#!/bin/bash
. /etc/init.d/functions
SRC_DIR=`pwd`
#MYSQL='mysql-5.7.33-linux-glibc2.12-x86_64.tar.gz'
MYSQL='mysql-5.7.35-linux-glibc2.12-x86_64.tar.gz'
COLOR='echo -e \E[01;31m'
END='\E[0m'
MYSQL_ROOT_PASSWORD=admin


check (){

if [ $UID -ne 0 ]; then
  action "当前用户不是root,安装失败" false
  exit 1
fi

cd  $SRC_DIR
`wget -c https://repo.huaweicloud.com/mysql/Downloads/MySQL-5.7/mysql-5.7.35-linux-glibc2.12-x86_64.tar.gz`
if [ !  -e $MYSQL ];then
        $COLOR"缺少${MYSQL}文件"$END
    $COLOR"请将相关软件放在${SRC_DIR}目录下"$END
        exit
elif [ -e /usr/local/mysql ];then
        action "数据库已存在，安装失败" false
        exit
else
  return
fi
} 
 
install_mysql(){
    $COLOR"开始安装MySQL数据库..."$END
    yum -y -q install chkconfig libncurses* libaio numactl-libs   libaio &> /dev/null
    cd $SRC_DIR
    tar xf $MYSQL -C /usr/local/
    MYSQL_DIR=`echo $MYSQL| sed -nr 's/^(.*[0-9]).*/\1/p'`
    ln -s  /usr/local/$MYSQL_DIR /usr/local/mysql
    ln -s /usr/lib64/libtinfo.so.6.1 /usr/lib64/libtinfo.so.5
    mkdir -p /data/mysql
    chown -R  root.root /usr/local/mysql/
    id mysql &> /dev/null || { useradd -s /sbin/nologin -r  mysql ; action "创建mysql用户"; }
        
    echo 'PATH=/usr/local/mysql/bin/:$PATH' > /etc/profile.d/mysql.sh
    .  /etc/profile.d/mysql.sh
  ln -s /usr/local/mysql/bin/* /usr/bin/
    cat > /etc/my.cnf <<-EOF
[mysqld]
server-id=1
log-bin
datadir=/data/mysql
socket=/data/mysql/mysql.sock                                                                                                   
log-error=/data/mysql/mysql.log
pid-file=/data/mysql/mysql.pid
[client]
socket=/data/mysql/mysql.sock
EOF
    mysqld --initialize --user=mysql --datadir=/data/mysql 
    cp /usr/local/mysql/support-files/mysql.server  /etc/init.d/mysqld
    chkconfig --add mysqld
    chkconfig mysqld on
    service mysqld start
    [ $? -ne 0 ] && { $COLOR"数据库启动失败，退出!"$END;exit; }
    MYSQL_OLDPASSWORD=`awk '/A temporary password/{print $NF}' /data/mysql/mysql.log`
    mysqladmin  -uroot -p$MYSQL_OLDPASSWORD password $MYSQL_ROOT_PASSWORD &>/dev/null
    action "数据库安装完成" 
}
 
 
check
 
install_mysql
```

运行mysql的命令报错：mysql: error while loading shared libraries: libncurses.so.5: cannot open shared object file: No such file or directory。

解决办法
```bash
yum install libncurses*
```

### 3.2 使用apt的系统
```bash
#!/bin/bash
SRC_DIR=`pwd`
MYSQL='mysql-5.7.30-linux-glibc2.12-x86_64.tar.gz'
MYSQL_DOWNLOAD_URL="https://repo.huaweicloud.com/mysql/Downloads/MySQL-5.7/mysql-5.7.30-linux-glibc2.12-x86_64.tar.gz"
Green_font_prefix="\033[32m"
Red_font_prefix="\033[31m"
Green_background_prefix="\033[42;37m"
Red_background_prefix="\033[41;37m"
Font_color_suffix="\033[0m"
Info="[${Green_font_prefix}信息${Font_color_suffix}]"
Error="[${Red_font_prefix}错误${Font_color_suffix}]"
Tip="[${Green_font_prefix}注意${Font_color_suffix}]"

MYSQL_ROOT_PASSWORD=czyadmin

check_root() {
    [[ $EUID != 0 ]] && echo -e "${Error} 当前非ROOT账号(或没有ROOT权限)，无法继续操作，请更换ROOT账号或使用 ${Green_background_prefix}sudo su${Font_color_suffix} 命令获取临时ROOT权限（执行后可能会提示输入当前账号的密码）。" && exit 1
}

check_is_installed() {
  if [ !  -e $MYSQL ];then
    echo -e "${Error} 缺少${MYSQL}文件"
    echo -e "${Error} 请将相关软件放在${SRC_DIR}目录下"
    exit 1
  elif [ -e /usr/local/mysql ];then
    echo -e "${Error} 数据库已存在，安装失败"
    exit 1
  else
    return
  fi
}

download_mysql() {
  cd  $SRC_DIR
  if [ !  -e $MYSQL ];then
    echo -e "${Tip} ${MYSQL}文件已下载到${SRC_DIR}目录下"
  else
    wget "${MYSQL_DOWNLOAD_URL}"
  fi
}

install_mysql() {
  echo -e "${Info} 开始安装MySql......"
  echo -e "${Info} 开始安装依赖..."
  apt install sysv-rc-conf libaio.* libnuma.* libncurses.* -y &> /dev/null
  echo -e "${Info} 依赖安装完成，开始解压MySql文件..."
  cd $SRC_DIR
  tar zxf $MYSQL -C /usr/local/
  echo -e "${Info} 解压完成，开始配置MySql环境..."
  MYSQL_DIR_NAME=`echo $MYSQL| sed -nr 's/^(.*[0-9]).*/\1/p'`
  mv /usr/local/$MYSQL_DIR_NAME /usr/local/mysql
  groupadd mysql
  useradd -g mysql mysql
  mkdir /usr/local/mysql/data
  chown -R  mysql.mysql /usr/local/mysql/
  echo 'PATH=/usr/local/mysql/bin/:$PATH' > /etc/profile.d/mysql.sh
  `source /etc/profile`
  cat > /etc/my.cnf <<-EOF
[mysql]
# 设置mysql字符集
default-character-set=utf8
socket=/var/lib/mysql/mysql.sock
[mysqld]
skip-name-resolve
#设置3306端口
port = 3306
socket=/var/lib/mysql/mysql.sock
# 设置mysql的安装目录
basedir=/usr/local/mysql
# 设置mysql数据库的数据存放目录
datadir=/usr/local/mysql/data
# 设置最大连接数
max_connections=200
# 设置默认字符集
character-set-server=utf8
# 创建新表时将使用的默认存储引擎
default-storage-engine=INNODB
lower_case_table_names=1
max_allowed_packet=16M
EOF

  mkdir /var/lib/mysql
  chmod 777 /var/lib/mysql
  basedir=/usr/local/mysql
  datadir=/usr/local/mysql/data
  cd $basedir
  ./bin/mysqld --initialize --user=mysql --basedir=/usr/local/mysql --datadir=/usr/local/mysql/data
  cp ./support-files/mysql.server /etc/init.d/mysqld
  MYSQL_OLDPASSWORD=`awk '/A temporary password/{print $NF}' /usr/local/mysql/data/mysql.log`
  echo $MYSQL_OLDPASSWORD > /usr/local/mysql/data/init_password.txt
  chmod +x /etc/init.d/mysqld
  sysv-rc-conf --add mysqld
  sysv-rc-conf mysqld on
  systemctl daemon-reload
    service mysqld start
    ln -s /var/lib/mysql/mysql.sock /tmp/mysql.sock
    ./bin/mysqladmin  -uroot -p$MYSQL_OLDPASSWORD password $MYSQL_ROOT_PASSWORD &>/dev/null
    `source /etc/profile`
    echo -e "${Info} MySql安装成功! "
}


check_root

download_mysql

check_is_installed

install_mysql
```
