---
title: "Rust的下载和安装"
description: "Rust的下载和安装"
keywords: "Rust"

date: 2023-06-16T16:41:30+08:00
lastmod: 2023-06-16T16:41:30+08:00

categories:
  - Rust
tags:
  - Rust
---

### 下载
[官网地址](https://www.rust-lang.org/zh-CN/)
[下载地址](https://www.rust-lang.org/zh-CN/learn/get-started)
### 安装
> windows 推荐使用WSL安装

`Windows安装`
安装器下载地址
[https://static.rust-lang.org/rustup/dist/x86_64-pc-windows-gnu/rustup-init.exe](https://static.rust-lang.org/rustup/dist/x86_64-pc-windows-gnu/rustup-init.exe)
工具链选择 x86_64-pc-windows-gnu 其它的默认就行
配置系统变量环境
CARGO_HOME	C:\Users\Colzry\.cargo
RUSTUP_HOME	C:\Users\Colzry\.rustup
编辑PATH	%CARGO_HOME%\bin
`WSL安装`
```shell
curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh

# 使用代理进行安装
curl --proto '=https' --tlsv1.2 -sSf https://rsproxy.cn/rustup-init.sh | sh
```

> Linux 安装

```bash
curl https://sh.rustup.rs -sSf | sh
```
### Rust 代理
[代理网站](https://rsproxy.cn/)
#### Rustup Mirror
vim $HOME/.cargo/env
```shell
########################### 字节镜像 ###################################
export RUSTUP_DIST_SERVER="https://rsproxy.cn"
export RUSTUP_UPDATE_ROOT="https://rsproxy.cn/rustup"

########################### 科大镜像 ###################################
export RUSTUP_DIST_SERVER="https://mirrors.ustc.edu.cn/rust-static"
export RUSTUP_UPDATE_ROOT="https://mirrors.ustc.edu.cn/rust-static/rustup"


########## Windows 下对应的设置环境变量的 PowerShell 命令为 ###########
$env:RUSTUP_DIST_SERVER="https://mirrors.ustc.edu.cn/rust-static"
$env:RUSTUP_UPDATE_ROOT="https://mirrors.ustc.edu.cn/rust-static/rustup"
```
#### crates.io Mirror
vim $HOME/.cargo/config
```shell
########################### 字节镜像 ###################################
[source.crates-io]
replace-with = 'rsproxy'

[source.rsproxy]
registry = "https://rsproxy.cn/crates.io-index"

[registries.rsproxy]
index = "https://rsproxy.cn/crates.io-index"

[net]
git-fetch-with-cli = true



########################### 科大镜像 ###################################
[source.crates-io]
replace-with = 'ustc'

[source.ustc]
registry = "https://mirrors.ustc.edu.cn/crates.io-index"
```


### crm Cargo镜像管理
> 不需要手动更改配置文件，使用命令行进行配置

官网：https://github.com/wtklbm/crm

```bash
# 安装
cargo install crm

# 测试国内镜像速度
crm test

# 切换最佳的镜像
crm best
```

### Rust圣经下载到本地阅读

```bash
git clone https://github.com/sunface/rust-course.git

cd ./rust-course

cargo install mdbook

mdbook build

cp ./assets/CNAME ./book/
cp ./assets/*.html ./book/
cp ./assets/sitemap.xml ./book/

mdbook serve
```
