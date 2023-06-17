---
title: "byobu的使用"
description: "byobu的使用"
keywords: "byobu"

date: 2023-06-16T16:41:30+08:00
lastmod: 2023-06-16T16:41:30+08:00

categories:
  - Linux
tags:
  - Linux
  - 工具使用
---

### 1.1 安装

`sudo apt-get install byobu` 命令安装byobu

### 1.2 登录启动

`byobu-enable` 表示Byobu窗口管理器将在每次文本登录时自动启动  
`byobu-disable` 表示Byobu窗口管理器将不再在登录时自动启动

### 1.3 色彩提示

`byobu-enable-prompt` 启动Byobu的彩色提示  
`byobu-disable-prompt` 禁用Byobu的彩色提示

## 2.Byobu 使用

> ##### 更多相关操作可以按 `F9` 选项查看帮助指南

### 2.1 使用会话

一个会话仅仅是byobu的运行实例。会话由一组窗口组成，这些窗口基本上是shell会话，默认开启byobu只开0这一个窗口

#### (1) 创建窗口

`F2` 创建新的窗口

#### (2) 切换窗口

`F3` 回到先前窗口  
`F4` 跳到下一个窗口

#### (3) 重命名窗口

`F8` 重新命名一个窗口

#### (4) 窗口操作

`F5` 重新加载文件  
`F6` 释放该次对话  
`F7` 进入 复制/回滚模式  
`F9` 配置菜单  
`Ctrl + D`关闭当前窗口

### 2.2 使用窗格

Byobu提供了将窗口分成多个窗格的功能，包括水平和垂直分割。这些允许您在同一窗口中进行多任务，而不是跨多个窗口。

#### (1) 创建窗格

`SHIFT + F2` 创建一个水平窗格  
`CTRL + F2` 创造一个垂直窗格

#### (2) 切换窗格

以下三个快捷键都可以让窗格切换:  
`SHIFT + LEFT/RIGHT/UP/DOWN`  
`SHIFT + F3/F4`  
`CTRL + F3/F4`

#### (3) 设置窗格

`SHIFT + ALT + LEFT/RIGHT/UP/DOWN` 调整当前窗格的大小  
`SHIFT + F11` 切换窗格以暂时填充整个窗口  
`ALT + F11` 永久地将窗格拆分为自己的新窗口

### 2.3 配置通知栏

①.按`F9`进入Byobu配置菜单。

②.导航到 `切换状态通知`选项，然后按 `ENTER`

③.选择要启用或禁用的状态通知。(启用状态通知后，它们将显示在底部状态栏中，与窗口指示器一起显示。)

④.选择要启用的状态通知后，选择 `应用`，按 `F5` 才能刷新状态栏

有很多不同的通知可供选择，一些常用的通知是：  
`date` 显示当前系统日期。  
`disk` 显示当前磁盘空间使用情况。  
`hostname` 显示当前系统主机名。  
`ip_address` 显示当前系统的IP地址。  
`load_average` 显示当前系统负载平均值。  
`memory` 显示当前的内存使用情况。  
`network` 显示当前的网络使用情况，发送和接收。  
`reboot_required` 显示需要重新启动系统时的指示灯。  
`release` 显示当前的分发版本（例如14.04）。  
`time` 显示当前系统时间。  
`updates_available` 在有可用更新时显示指示符。  
`uptime` 显示当前系统正常运行时间。  
`whoami` 显示当前登录的用户。