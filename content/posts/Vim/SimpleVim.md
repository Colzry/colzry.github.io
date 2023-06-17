---
title: "自用Vim配置 SimpleVim"
description: "SimpleVim轻量化的Vim配置，可用于编写查看小项目，带自动提示"
keywords: "Shell"

date: 2023-06-16T16:41:30+08:00
lastmod: 2023-06-16T16:41:30+08:00

categories:
  - Vim
tags:
  - Vim
---

![](https://picture-czy.oss-cn-beijing.aliyuncs.com/img/202205172038208.png#crop=0&crop=0&crop=1&crop=1&id=PpSc6&originHeight=731&originWidth=1123&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

### 安装

```shell
git clone https://gitee.com/colzry/SimpleVim.git
git clone https://github.com/Colzry/SimpleVim.git
cd SimpleVim/
bash install.sh
```

### 卸载

```shell
cd ~/simple-vim/
bash uninstall.sh
```

### 使用

```bash
vv或者kj     退出插入模式
Ctrl+a		全选
U			光标向下移动5行=5j
E			光标向上移动5行=5k
=			查找模式时，下一个匹配值
-			查找模式时，上一个匹配值
<Leader>+1	   移动到行首
<Leader>+0	   移动到行尾
S			保存文件
Q    		退出vim编辑器
R			刷新vimrc文件
s]			水平向右分割窗口
s[        	水平向左分割窗口
sk			垂直向上分割窗口
sj			垂直向下分割窗口
<Leader>+h	   光标移动到左窗口
<Leader>+l	   光标移动到右窗口
<Leader>+j	   光标移动到下窗口
<Leader>+k	   光标移动到上窗口
<Leader>+↑	   光标所在窗口向上拉长
<Leader>+↓	   光标所在窗口向下拉长
<Leader>+←	   光标所在窗口向左拉长
<Leader>+→	   光标所在窗口向右拉长
<Leader>+w	   删除光标所在单词
<Leader>+u		向下翻半屏
<Leader>+d		向上翻半屏
<Leader>+n		打开文件树形结构列表
<Leader>+m		查看文件函数列表
{            光标向上移动一个代码块
}            光标向下移动一个代码块
di(          删除括号里的内容
di{          删除花括号里的内容
:e 文档名     进入vim后，不离开 vim 的情形下打开其他文档
:files 或 :buffers 或 :ls     可以列出目前 缓冲区 中的所有文档。加号 + 表示 缓冲区已经被修改过了。＃代表上一次编辑的文档，%是目前正在编辑中的文档
:b 文档名或编号      移至该文档
<Leader>+cc			注释当前行
<Leader>+cu			取消注释
```
