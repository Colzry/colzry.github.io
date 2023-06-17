---
title: "Debian Locale问题"
description: "Debian Local问题"
keywords: "Locale"

date: 2023-06-16T16:41:30+08:00
lastmod: 2023-06-16T16:41:30+08:00

categories:
  - Linux
tags:
  - Linux
  - 疑难杂症
---

# Debian locale问题

## 问题

```bash
root@debian:~# locale -a
locale: Cannot set LC_CTYPE to default locale: No such file or directory
locale: Cannot set LC_MESSAGES to default locale: No such file or directory
locale: Cannot set LC_COLLATE to default locale: No such file or directory
C
C.UTF-8
POSIX
zh_CN.utf8

-bash: warning: setlocale: LC_ALL: cannot change locale (en_US.UTF-8)
```

## 解决办法
```bash
sudo localedef -i en_US -f UTF-8 en_US.UTF-8
```