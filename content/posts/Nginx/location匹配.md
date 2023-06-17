---
title: "Nginx location匹配"
description: "Nginx location匹配"
keywords: "Nginx"

date: 2023-06-16T16:41:30+08:00
lastmod: 2023-06-16T16:41:30+08:00

categories:
  - Nginx
tags:
  - Nginx
---



**location语法**
location [=|~|~*|^~] /url/ { ... }
注：uri是指匹配路径， [ =| ~ | ~* | ^~ | @ ] 是指匹配规则（可选）

= : 完全匹配，表示精确匹配后面的url
^~ : 无正则普通匹配（ ^ 表示“非”，~ 表示“正则”，字符意思是：不会继续匹配正则），表示普通字符匹配，如果该选项匹配，只匹配该选项，不匹配别的选项，一般用来匹配目录
/ : 普通匹配（/xxx）
~ : 表示正则匹配，区分大小写
~* : 表示正则匹配，不区分大小写
@ : “@” 定义一个命名的 location，使用在内部定向时，例如 error_page

= 进行普通字符精确匹配。也就是完全匹配。
^~ 前缀匹配。如果匹配成功，则不再匹配其他location。
~ 表示执行一个正则匹配，区分大小写
~* 表示执行一个正则匹配，不区分大小写
/xxx/ 常规字符串路径匹配
/ 通用匹配，任何请求都会匹配到

## 1. 匹配的先后顺序

1. = 前缀的指令严格匹配这个查询，优先级最高，一旦匹配成功，则停止搜索其他localtion的匹配项；
2. 所有剩下的常规字符串（^~和普通匹配），‘最长命中’规则，优先使用匹配最长的结果。
3. 正则表达式，在配置文件中定义的顺序
> 注： ^~ 和普通匹配。使用前缀匹配，不支持正则表达式，如果有多个location匹配成功的话，不会终止匹配过程，会记忆表达式最长的那个，如果得到的最长的location为 ^~ 类型，则表示阻断正则表达式，不再匹配正则表达式。如果得到的最长的location不是 ^~ 类型，继续匹配正则表达式，只要有一个正则成功，则使用这个正则的location，立即返回结果，并结束解析过程。

```nginx
location = / {
    # 精确匹配/，主机名后面不能带任何字符串 /
    [ configuration A ]
}
location / {
    # 匹配所有以 / 开头的请求。
    # 但是如果有更长的同类型的表达式，则选择更长的表达式。
    # 如果有正则表达式可以匹配，则优先匹配正则表达式。
    [ configuration B ]
}
location /documents/ {
    # 匹配所有以 /documents/ 开头的请求，匹配符合以后，还要继续往下搜索。
    # 但是如果有更长的同类型的表达式，则选择更长的表达式。
    # 如果有正则表达式可以匹配，则优先匹配正则表达式。
    [ configuration C ]
}
location ^~ /images/ {
    # 匹配所有以 /images/ 开头的表达式，如果匹配成功，则停止匹配查找，停止搜索。
    # 所以，即便有符合的正则表达式location，也不会被使用
    [ configuration D ]
}

location ~* \.(gif|jpg|jpeg)$ {
    # 匹配所有以 gif jpg jpeg结尾的请求。
    # 但是 以 /images/开头的请求，将使用 Configuration D，D具有更高的优先级
    [ configuration E ]
}

location /images/ {
    # 字符匹配到 /images/，还会继续往下搜索
    [ configuration F ]
}


location = /test.htm {
    root   /usr/local/var/www/htm;
    index  index.htm;
}
```
[https://blog.csdn.net/beichengliulixue/article/details/121971227](https://blog.csdn.net/beichengliulixue/article/details/121971227)
[https://zhuanlan.zhihu.com/p/389438482](https://zhuanlan.zhihu.com/p/389438482)
