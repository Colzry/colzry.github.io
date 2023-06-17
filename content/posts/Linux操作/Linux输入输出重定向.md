---
title: "Linux输入输出重定向"
description: "Linux输入输出重定向"
keywords: "重定向"

date: 2023-06-16T16:41:30+08:00
lastmod: 2023-06-16T16:41:30+08:00

categories:
  - Linux
tags:
  - Linux
---

### 说明
|  |  |
| --- | --- |
| 0 | 标准输入 |
| 1 | 标准输出 |
| 2 | 标准错误 |


下面命令表示把输出结果重定向到file文件中，而错误重定向到标准输出，此时的标准输出为重定向到file文件中，所以错误也会输出到file文件中

```bash
command > file 2>&1
```

### 解释

何2>&1要写在后面？

command > file 2>&1

首先是command > file将标准输出重定向到file中， 2>&1 是标准错误拷贝了标准输出的行为，也就是同样被重定向到file中，最终结果就是标准输出和错误都被重定向到file中。

command 2>&1 >file

2>&1 标准错误拷贝了标准输出的行为，但此时标准输出还是在终端。>file 后输出才被重定向到file，但标准错误仍然保持在终端。

用strace可以看到：

1. command > file 2>&1

这个命令中实现重定向的关键系统调用序列是：

open(file) == 3

dup2(3,1)

dup2(1,2)

1. command 2>&1 >file

这个命令中实现重定向的关键系统调用序列是：

dup2(1,2)

open(file) == 3

dup2(3,1)

> **为什么会有&**


当没有&时，1会被认为是一个普通的文件，有&表示重定向的目标不是一个文件，而是一个文件描述符。

> **为什么有&1而没有&2**


2>是一个整体，表示标准错误输出重定向，重定向至&1，即标准输出，&1是一个文件

### 常用命令

挂入后台命令

```bash
# 普通 断开终端就停止
command >/dev/null 2>&1 &

# 高级 断开终端不停止
nohup command >/dev/null 2>&1 &
# 或者
nohup command >& /dev/null &
```

### 其他写法

```bash
command > file 2>&1  

等价写法

command >& file 

command &> file
```
