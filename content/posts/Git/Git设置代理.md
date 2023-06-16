---
title: "Git设置代理"
description: "git"
keywords: "git"

date: 2023-06-16T16:41:30+08:00
lastmod: 2023-06-16T16:41:30+08:00

categories:
  - Git
tags:
  - Git
---

```bash
# 以下使用http代理 
git config --global http.proxy http://127.0.0.1:10809 
git config --global https.proxy https://127.0.0.1:10809 

# 以下使用socks5代理 
git config --global http.proxy socks5://127.0.0.1:10808
git config --global https.proxy socks5://127.0.0.1:10808

# 取消代理 
git config --global --unset http.proxy 
git config --global --unset https.proxy
```

```bash
git config --global url."https://gitpy.colzry.tk/".insteadOf https://

git config --global --remove-section url."https://gitpy.colzry.tk/"
```