---
title: "编译安装Nginx"
description: "编译安装Nginx"
keywords: "Nginx"

date: 2023-06-16T16:41:30+08:00
lastmod: 2023-06-16T16:41:30+08:00

categories:
  - Nginx
tags:
  - Nginx
---

# 编译安装Nginx

## 1. 卸载原有的Nginx

```bash
dnf remove nginx nginx-common
```

## 2. 安装编译依赖

### 2.1 安装编译工具

```bash
dnf install make automake gcc gcc-c++ kernel-devel
```

### 2.2 安装编译时需要的依赖

```bash
dnf install -y pcre pcre-devel pcre2 pcre2-devel zlib zlib-devel openssl openssl-devel libxslt-devel
```

### 2.3 拉取需要编译的模块

```bash
git clone https://github.com/arut/nginx-dav-ext-module
```

## 3. 下载并解压nginx

```bash
wget http://nginx.org/download/nginx-1.23.3.tar.gz && tar -zvxf nginx-1.23.3.tar.gz
```

##　4. 编译nginx

### 4.1 创建nginx用户

> 注意： 后面并没有使用nginx用户启动，需要自行配置

```bash
#创建nginx用户与用户组
useradd -s /sbin/nologin nginx
```

### 4.2 做编译前的配置

```bash
./configure \
--prefix=/usr \
--sbin-path=/usr/sbin/nginx \
--conf-path=/etc/nginx/nginx.conf \
--error-log-path=/var/log/nginx/error.log \
--http-log-path=/var/log/nginx/access.log \
--pid-path=/var/run/nginx/nginx.pid \
--lock-path=/var/lock/nginx.lock \
--user=nginx \
--group=nginx \
--with-http_gzip_static_module \
--with-http_ssl_module \
--with-http_stub_status_module \
--with-http_flv_module \
--with-http_dav_module --add-module=../nginx-dav-ext-module
```

configure参数解释:

```bash
--prefix=/usr/                                   # 指向安装目录
--sbin-path=/usr/sbin/nginx \                    # 指向（执行）程序文件（nginx）
--conf-path=/etc/nginx/nginx.conf                # 指定配置文件
--http-log-path=/var/log/nginx/access.log        # 指定访问日志
--error-log-path=/var/log/nginx/error.log        # 指定错误日志
--lock-path=/var/lock/nginx.lock                 # 指定lock文件
--pid-path=/var/run/nginx/nginx.pid              # 指定pid文件
--user=nginx \                                   # 指定用户
--group=nginx \                                  # 指定用户组
--http-client-body-temp-path=/var/lib/nginx/body    # 设定http客户端请求临时文件路径
--http-fastcgi-temp-path=/var/lib/nginx/fastcgi     # 设定http fastcgi临时文件路径
--http-proxy-temp-path=/var/lib/nginx/proxy         # 设定http代理临时文件路径
--http-scgi-temp-path=/var/lib/nginx/scgi           # 设定http scgi临时文件路径
--http-uwsgi-temp-path=/var/lib/nginx/uwsgi         # 设定http uwsgi临时文件路径
--with-pcre                                         # 启用pcre库
--with-debug                                        # 启用debug日志
--with-pcre-jit                                     # 编译PCRE包含“just-in-time compilation”
--with-ipv6                                         # 启用ipv6支持
--with-http_ssl_module                              # 启用ssl支持
--with-http_stub_status_module                      # 获取nginx自上次启动以来的状态
--with-http_realip_module                 # 允许从请求标头更改客户端的IP地址值，默认为关
--with-http_auth_request_module           # 实现基于一个子请求的结果的客户端授权。如果该子请求返回的2xx响应代码，所述接入是允许的。如果它返回401或403中，访问被拒绝与相应的错误代码。由子请求返回的任何其他响应代码被认为是一个错误。
--with-http_addition_module               # 作为一个输出过滤器，支持不完全缓冲，分部分响应请求
--with-http_dav_module                    # 增加PUT,DELETE,MKCOL：创建集合,COPY和MOVE方法 默认关闭，需编译开启
--with-http_geoip_module                  # 使用预编译的MaxMind数据库解析客户端IP地址，得到变量值
--with-http_gunzip_module                 # 它为不支持“gzip”编码方法的客户端解压具有“Content-Encoding: gzip”头的响应。
--with-http_gzip_static_module            # 在线实时压缩输出数据流
--with-http_image_filter_module           # 传输JPEG/GIF/PNG 图片的一个过滤器）（默认为不启用。gd库要用到）
--with-http_spdy_module                   # SPDY可以缩短网页的加载时间
--with-http_sub_module                    # 允许用一些其他文本替换nginx响应中的一些文本
--with-http_xslt_module                   # 过滤转换XML请求
--with-mail                               # 启用POP3/IMAP4/SMTP代理模块支持
--with-mail_ssl_module                    # 启用ngx_mail_ssl_module支持启用外部模块支持
```

### 4.3 编译并安装

```bash
make && make install
```

##　5．为 nginx 创建 systemd 单元

```bash
vim /etc/systemd/system/nginx.service

[Unit]
Description=nginx
Documentation=https://nginx.org/en/docs/
After=network-online.target remote-fs.target nss-lookup.target
Wants=network-online.target

[Service]
Type=forking
PIDFile=/var/run/nginx/nginx.pid
ExecStartPre=/usr/sbin/nginx -t -c /etc/nginx/nginx.conf
ExecStart=/usr/sbin/nginx -c /etc/nginx/nginx.conf
ExecReload=/bin/kill -s HUP $MAINPID
ExecStop=/bin/kill -s TERM $MAINPID

[Install]
WantedBy=multi-user.target
```

之后使用 systemd 即可控制 nginx 服务的启动，停止等

```bash
systemctl daemon-reload
systemctl start nginx
systemctl status nginx

# 使用ss查看端口
[root@localhost]# ss -lnt
State       Recv-Q      Send-Q           Local Address:Port           Peer Address:Port      Process      
LISTEN      0           511                    0.0.0.0:80                  0.0.0.0:*                      
LISTEN      0           128                    0.0.0.0:22                  0.0.0.0:*                      
LISTEN      0           128                       [::]:22                     [::]:* 
```



