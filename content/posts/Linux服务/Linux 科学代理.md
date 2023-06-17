---
title: "Linux科学代理"
description: "Linux科学代理"
keywords: "代理"

date: 2023-06-16T16:41:30+08:00
lastmod: 2023-06-16T16:41:30+08:00

categories:
  - Linux
tags:
  - Linux
  - 服务搭建
  - 代理
---

官方文档： [https://v2raya.org/docs/prologue/quick-start/](https://v2raya.org/docs/prologue/quick-start/)
安装v2ray内核，使用镜像脚本（不通过添加软件源安装）
```bash
# v2rayA 提供的镜像脚本
curl -Ls https://mirrors.v2raya.org/go.sh | sudo bash
# 安装后可以关掉服务，因为 v2rayA 不依赖于该 systemd 服务
sudo systemctl disable v2ray --now
```
## 1. 安装
### 1.1 Fedora
```bash
# 添加 copr 源
sudo dnf copr enable zhullyb/v2rayA
# 安装 V2Ray 内核
sudo dnf install v2ray-core
# 安装 v2rayA
sudo dnf install v2raya

# 启动
sudo systemctl start v2raya.service
# 设置开机自启
sudo systemctl enable v2raya.service
```
### 1.2 Debian
请先通过顶上的方法安装v2ray内核
```bash
# 添加公钥
wget -qO - https://apt.v2raya.org/key/public-key.asc | sudo tee /etc/apt/trusted.gpg.d/v2raya.asc

# 添加 V2RayA 软件源
echo "deb https://apt.v2raya.org/ v2raya main" | sudo tee /etc/apt/sources.list.d/v2raya.list
sudo apt update

# 安装 V2RayA
sudo apt install v2raya

# 启动
sudo systemctl start v2raya.service
# 设置开机自启 
sudo systemctl enable v2raya.service
```

切换 iptables 为 iptables-nft
对于 Debian11 用户来说，iptables 已被弃用。使用 nftables 作为 iptables 的后端以进行适配
```bash
update-alternatives --set iptables /usr/sbin/iptables-nft
update-alternatives --set ip6tables /usr/sbin/ip6tables-nft
update-alternatives --set arptables /usr/sbin/arptables-nft
update-alternatives --set ebtables /usr/sbin/ebtables-nft
```
如果你想切换回 legacy 版本
```bash
update-alternatives --set iptables /usr/sbin/iptables-legacy
update-alternatives --set ip6tables /usr/sbin/ip6tables-legacy
update-alternatives --set arptables /usr/sbin/arptables-legacy
update-alternatives --set ebtables /usr/sbin/ebtables-legacy
```
切换后重启即可

## 2. 使用
通过 2017 端口 如 [http://localhost:2017](http://localhost:2017)访问 UI 界面
记得调整防火墙
### 2.1 创建管理员账号
![image.png](https://cdn.nlark.com/yuque/0/2022/png/22223333/1663728117310-565fd5e4-4392-42c5-8da7-cdc6bec6164f.png#clientId=u967c4c27-84fc-4&crop=0&crop=0&crop=1&crop=1&from=paste&height=645&id=u31e9f06c&margin=%5Bobject%20Object%5D&name=image.png&originHeight=968&originWidth=743&originalType=binary&ratio=1&rotation=0&showTitle=false&size=71775&status=done&style=none&taskId=u424eb700-c75a-486b-9a7f-df29209e28b&title=&width=495.3333333333333)
### 2.2 导入节点
![image.png](https://cdn.nlark.com/yuque/0/2022/png/22223333/1663728187878-f6bda573-ab16-4648-9a82-e5a0e7d5e2a9.png#clientId=u967c4c27-84fc-4&crop=0&crop=0&crop=1&crop=1&from=paste&height=264&id=ude31bcdb&margin=%5Bobject%20Object%5D&name=image.png&originHeight=396&originWidth=789&originalType=binary&ratio=1&rotation=0&showTitle=false&size=42877&status=done&style=none&taskId=u63a5ce6b-30c1-47aa-b464-e5ad147d83c&title=&width=526)
```bash
vmess://ew0KICAidiI6ICIyIiwNCiAgInBzIjogIlZtZXNzIiwNCiAgImFkZCI6ICJwYW4uZ29zc2lwLnRrIiwNCiAgInBvcnQiOiAiNDQzIiwNCiAgImlkIjogIjcyNTQ0Nzk0LTRhYWUtNGVmNy1jMmRhLTUxOTRjN2RkOGI4NSIsDQogICJhaWQiOiAiMCIsDQogICJzY3kiOiAiYXV0byIsDQogICJuZXQiOiAid3MiLA0KICAidHlwZSI6ICJub25lIiwNCiAgImhvc3QiOiAicGFuLmdvc3NpcC50ayIsDQogICJwYXRoIjogIi9nb2ZpYyIsDQogICJ0bHMiOiAidGxzIiwNCiAgInNuaSI6ICIiLA0KICAiYWxwbiI6ICIiDQp9
```
![image.png](https://cdn.nlark.com/yuque/0/2022/png/22223333/1663728252858-a7bb1827-c378-4581-bec2-f5da1de95d49.png#clientId=u967c4c27-84fc-4&crop=0&crop=0&crop=1&crop=1&from=paste&height=339&id=ucd72734d&margin=%5Bobject%20Object%5D&name=image.png&originHeight=508&originWidth=2007&originalType=binary&ratio=1&rotation=0&showTitle=false&size=40236&status=done&style=none&taskId=u97077a7e-d277-44a4-a7a5-f1b1c41f288&title=&width=1338)
### 2.3 启动
![image.png](https://cdn.nlark.com/yuque/0/2022/png/22223333/1663728372471-eaba2a03-1979-452d-b426-1a3d284bdec9.png#clientId=u967c4c27-84fc-4&crop=0&crop=0&crop=1&crop=1&from=paste&height=393&id=u9f845931&margin=%5Bobject%20Object%5D&name=image.png&originHeight=589&originWidth=2197&originalType=binary&ratio=1&rotation=0&showTitle=false&size=68559&status=done&style=none&taskId=u13cc2f5a-26b0-4c72-9a82-df12b4d412f&title=&width=1464.6666666666667)
### 2.4 设置
![image.png](https://cdn.nlark.com/yuque/0/2022/png/22223333/1663728571325-298d2d49-ac7f-45a4-842a-ccc5dfd5da85.png#clientId=u967c4c27-84fc-4&crop=0&crop=0&crop=1&crop=1&from=paste&height=583&id=u8ce433ee&margin=%5Bobject%20Object%5D&name=image.png&originHeight=875&originWidth=987&originalType=binary&ratio=1&rotation=0&showTitle=false&size=86450&status=done&style=none&taskId=uf6382972-7651-4ca8-9926-5ab03df51b0&title=&width=658)
### 2.5 开放代理端口
![image.png](https://cdn.nlark.com/yuque/0/2022/png/22223333/1663728728774-05f1ae70-ff7a-4770-b256-8aeec9af9606.png#clientId=u967c4c27-84fc-4&crop=0&crop=0&crop=1&crop=1&from=paste&height=550&id=u7f58547c&margin=%5Bobject%20Object%5D&name=image.png&originHeight=825&originWidth=1260&originalType=binary&ratio=1&rotation=0&showTitle=false&size=81840&status=done&style=none&taskId=u1763b769-c369-4818-bc06-d2cb78e465f&title=&width=840)

### 2.6 设置Linux代理
```bash
export ALL_PROXY=socks5://127.0.0.1:20170

# 取消代理
unset ALL_PROXY
```
### 2.7 使用proxychains设置代理（推荐）
安装
```bash
# Fedora
sudo dnf install proxychains-ng

# Debian
sudo apt install proxychains-ng
```
配置
```bash
vim /etc/proxychains.conf

socks5 127.0.0.1 20170
```
测试一下
```bash
[root@fedora ~]# proxychains curl cip.cc
[proxychains] config file found: /etc/proxychains.conf
[proxychains] preloading /usr/lib64/proxychains-ng/libproxychains4.so
[proxychains] DLL init: proxychains-ng 4.16
[proxychains] Strict chain  ...  127.0.0.1:20170  ...  cip.cc:80  ...  OK
IP      : 198.23.149.5
地址    : 美国  华盛顿州  西雅图
运营商  : colocrossing.com

数据二  : 美国 | 纽约州伊利县威廉斯维尔村ColoCrossing有限公司

数据三  : 美国华盛顿西雅图

URL     : http://www.cip.cc/198.23.149.5
```
简化使用命令
```bash
echo "alias pc='proxychains'" >> ~/.bashrc
source ~/.bashrc

[root@fedora ~]# pc curl cip.cc
[proxychains] config file found: /etc/proxychains.conf
[proxychains] preloading /usr/lib64/proxychains-ng/libproxychains4.so
[proxychains] DLL init: proxychains-ng 4.16
[proxychains] Strict chain  ...  192.168.211.99:20170  ...  122.51.162.249:80  ...  OK
IP      : 198.23.149.5
地址    : 美国  华盛顿州  西雅图
运营商  : colocrossing.com

数据二  : 美国 | 纽约州伊利县威廉斯维尔村ColoCrossing有限公司

数据三  : 美国华盛顿西雅图

URL     : http://www.cip.cc/198.23.149.5
```
### 2.8 设置局域网使用
![image.png](https://cdn.nlark.com/yuque/0/2022/png/22223333/1663730963472-02317f57-9a7f-450d-9e59-a9a816563533.png#clientId=u967c4c27-84fc-4&crop=0&crop=0&crop=1&crop=1&from=paste&height=527&id=u3b185e36&margin=%5Bobject%20Object%5D&name=image.png&originHeight=790&originWidth=956&originalType=binary&ratio=1&rotation=0&showTitle=false&size=85940&status=done&style=none&taskId=u55482049-481e-48ce-81a3-3708342fe4b&title=&width=637.3333333333334)
## 3. 浏览器代理
SwitchyOmega 等浏览器插件可为浏览器提供代理服务。
![image.png](https://cdn.nlark.com/yuque/0/2022/png/22223333/1663731716635-53967692-a26b-4a25-8364-c97ba7667536.png#clientId=u967c4c27-84fc-4&crop=0&crop=0&crop=1&crop=1&from=paste&height=221&id=u665f3874&margin=%5Bobject%20Object%5D&name=image.png&originHeight=332&originWidth=1540&originalType=binary&ratio=1&rotation=0&showTitle=false&size=41348&status=done&style=none&taskId=u72b864e5-02b1-4d77-8747-75820b6c494&title=&width=1026.6666666666667)
