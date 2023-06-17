---
title: "Tmux的使用"
description: "Tmux的使用"
keywords: "Tmux"

date: 2023-06-16T16:41:30+08:00
lastmod: 2023-06-16T16:41:30+08:00

categories:
  - Linux
tags:
  - Linux
  - 工具使用
---

# Tmux的使用

> **说明：Ctrl+b 为快捷方式的前缀键，即先按下Ctrl+b，快捷键才会生效。**


## 1. 会话管理

### 1.1 新建会话

```bash
tmux new -s <session-name>
```

### 1.2 分离会话

在 Tmux 窗口中，按下`Ctrl+b d`或者输入`tmux detach`命令，就会将当前会话与窗口分离。

```bash
tmux detach
```

上面命令执行后，就会退出当前 Tmux 窗口，但是会话和里面的进程仍然在后台运行。

tmux ls命令可以查看当前所有的 Tmux 会话。

```bash
tmux ls
# or
tmux list-session
```

### 1.3 接入会话

tmux attach命令用于重新接入某个已存在的会话

```bash
# 使用会话编号
tmux attach -t 0

# 使用会话名称
tmux attach -t <session-name>
```

### 1.4 杀死会话

tmux kill-session命令用于杀死某个会话

```bash
# 使用会话编号
tmux kill-session -t 0

# 使用会话名称
tmux kill-session -t <session-name>
```

### 1.5 切换会话

tmux switch命令用于切换会话

```bash
# 使用会话编号
tmux switch -t 0

# 使用会话名称
tmux switch -t <session-name>
```

### 1.6 重命名会话

tmux rename-session命令用于重命名会话。

```bash
tmux rename-session -t 0 <new-name>
```

### 1.7 会话快捷键

```bash
$ Ctrl+b d：分离当前会话。

$ Ctrl+b s：列出所有会话。

$ Ctrl+b $：重命名当前会话。
```

## 2. 窗口管理

### 2.1 新建窗口

```bash
tmux new-window

# 新建一个指定名称的窗口
tmux new-window -n <window-name>
```

### 2.2 切换窗口

```bash
# 切换到指定编号的窗口
tmux select-window -t <window-number>

# 切换到指定名称的窗口
tmux select-window -t <window-name>
```

### 2.3 重命名窗口

```bash
tmux rename-window <new-name>
```

### 2.4 窗口快捷键

```bash
Ctrl+b c：创建一个新窗口，状态栏会显示多个窗口的信息。 
Ctrl+b p：切换到上一个窗口（按照状态栏上的顺序）。 
Ctrl+b n：切换到下一个窗口。 
Ctrl+b ：切换到指定编号的窗口，其中的是状态栏上的窗口编号。 
Ctrl+b w：从列表中选择窗口。 
Ctrl+b ,：窗口重命名。
Ctrl+b %：划分左右两个窗格。
Ctrl+b "：划分上下两个窗格。
Ctrl+b ：光标切换到其他窗格。是指向要切换到的窗格的方向键，比如切换到下方窗格，就按方向键↓。
Ctrl+b ;：光标切换到上一个窗格。
Ctrl+b o：光标切换到下一个窗格。
Ctrl+b {：当前窗格左移。
Ctrl+b }：当前窗格右移。
Ctrl+b Ctrl+o：当前窗格上移。
Ctrl+b Alt+o：当前窗格下移。
Ctrl+b x：关闭当前窗格。
Ctrl+b !：将当前窗格拆分为一个独立窗口。
Ctrl+b z：当前窗格全屏显示，再使用一次会变回原来大小。
Ctrl+b Ctrl+：按箭头方向调整窗格大小。
Ctrl+b q：显示窗格编号。
```

## 更改prefix前缀快捷键

修改系统级的/etc/tmux.conf或用户级的~/.tmux.conf，没有的话新建

向文件写入一下内容

```bash
set -g prefix C-x  
unbind C-b
bind C-x send-prefix
```
