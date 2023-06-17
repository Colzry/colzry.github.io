---
title: "Screen的使用"
description: "Screen的使用"
keywords: "Screen"

date: 2023-06-16T16:41:30+08:00
lastmod: 2023-06-16T16:41:30+08:00

categories:
  - Linux
tags:
  - Linux
  - 工具使用
---

```bash
# 新建会话
screen -S <session_name>
# 快捷键Ctrl + A + D 分离当前会话到后台，返回到用户终端
C-a + d
# 查看建立的会话
screen -ls
# 连接建立的会话
screen -r <session_name>
# 杀死建立的会话
screen -XS <session_name> quit
```
