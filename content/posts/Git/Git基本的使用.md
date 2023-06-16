---
title: "Git的基本使用"
description: "git"
keywords: "git"

date: 2023-06-16T16:41:30+08:00
lastmod: 2023-06-16T16:41:30+08:00

categories:
  - Git
tags:
  - Git
---

### 1. 设置签名
```shell
git config --global user.name tom #设置用户名tom
git config --global user.email xxx@qq.com #设置用户邮箱
```
### 2. 创建本地仓库
```shell
$ git init
```

### 3. 版本提交
#### 3.1 状态查看
```shell
git status   #查看工作区、暂存区状态
```
#### 3.2 添加
```shell
git add fileName  #指定文件
git add . #所有
说明：将工作区的文件添加到暂存区
```
#### 3.3 提交
```shell
git commit -m 'commit message'
说明：将暂存区内容提交到本地库
```
#### 3.4 查看历史记录
```shell
git log 
git reflog  #常用
git log --greph #图形显示,更直观
git log --pretty=oneline #漂亮一行显示
git log --oneline #简洁显示
说明：HEAD@{移动到当前版本需要多少步}
```

### 4. 分支操作
#### 4.1 创建分支
```shell
git branch 分支名
```
#### 4.2 查看分支
```shell
git branch
git branch -v
```
#### 4.3 切换分支
```shell
git checkout 分支名
git checkout -b 分支名   #创建分支并直接切换到该分支
```
#### 4.4 重命名分支
在当前分支
```shell
git branch -m new_branch_name
```

不在当前分支
```shell
git branch -m old_name new_name
```
#### 4.5 合并分支
`**相当于把修改了的文件拉过来**`
```shell
git rebase <branch>
将指定的分支合并到当前分支


git merge --no-ff xxx
注意：合并分支的时候要明确谁谁合并
	我在a分支里面修改了。要合并到master，就先切换到master，然后合并b

```
#### 4.6 删除分支
```shell
git branch -d 分支名
```
### 5. 使用远程仓库
#### 5.1 创建远程库地址别名
```shell
git remote -v  #查看远程地址别名
git remote add 别名 远程地址 
git remote set-url 别名 远程地址 
例子：git remote add origin https://xx
```
#### 5.2 推送
`**开发修改完把本地库的文件推送到远程仓库**`** **`**前提是提交到了本地库才可以推送**`
```shell
git push 别名 分支名
git push -u 别名 分支名    #-u指定默认主机
git push -f # 强制推送
例子：git push origin master
```
#### 5.3 克隆
`**完整的把远程库克隆到本地**`**  **`**克隆下来后不要在主分支里面做开发**`** **`**clone进行一次，从无到有的过程，更新用pull**`
```shell
git clone  远程地址
例子：git clone https://xx
```
#### 5.4 拉取
`**本地存在clone下来的文件 就用pull更新**`
```shell
pull = fetch + merge
	git fetch 别名 分支名
	git merge 别名 分支名
git pull 别名 分支名
```
#### 5.5 解决冲突
`注意：解决冲突后的提交是不能带文件名的`
`如果不是基于远程库最新版做的修改不能推送，必须先pull下来安装冲突办法解决`

```bash
# 查看所有分支
git reflog 
# 回退到上一个版本
git rest --hard HEAD^
# 回退到上上个版本
git rest --hard HEAD^^
# 回退到commit id为ba7914b的版本
git rest --hard ba7914b
```

```bash
git pull origin/master
# 合并之后修改冲突
git diff origin/master
```