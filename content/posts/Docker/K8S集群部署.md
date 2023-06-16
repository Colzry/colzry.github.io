---
title: "K8S集群部署"
date: 2023-06-16T10:10:26+08:00
categories:
 - Docker
tags:
  - Docker
---


官网地址：https://kubernetes.io/zh-cn/docs/setup/production-environment/tools/kubeadm/
## 1. 搭建单Master集群
1. 创建一个 Master 节点 `kubeadm init`
2. 将 Node节点加入到当前集群中 `kubeadm join <Master节点的IP和端口>`
环境准备

|  工作节点  |   主机名   |       IP地址      |    系统版本    |
| :-------: | :-------: | :--------------: | :-----------: |
| master    | k8s-master | 192.168.211.201 | almalinux8.6  |
| node1     | k8s-node1  | 192.168.211.202 | almalinux8.6  |
| node2     | k8s-node2  | 192.168.211.203 | almalinux8.6  |

> **注意：** 从 `2 - 5` 的内容在在`master`和`node`节点主机在都要执行
## 2. 安装前准备
### 2.1 修改和添加主机名
```bash
# 修改主机名 
# 在master节点执行
hostnamectl set-hostname k8s-master
# 在node节点执行
hostnamectl set-hostname k8s-node1
hostnamectl set-hostname k8s-node2

# 添加主机名 
cat >> /etc/hosts << EOF 
192.168.211.201 k8s-master 
192.168.211.202 k8s-node1 
192.168.211.203 k8s-node2 
EOF
```

### 2.2 关闭防火墙
```bash
systemctl stop firewalld 
systemctl disable firewalld
```
### 2.3 关闭 selinux
```bash
sed -i 's/SELINUX=enforcing/SELINUX=disabled/g' /etc/sysconfig/selinux
setenforce 0
```

### 2.4 关闭swap分区
```bash
先临时关闭，再永久关闭，这样就不用重启

# 临时关闭
swapoff -a
# 永久关闭
sed -ri 's/.*swap.*/#&/' /etc/fstab # 重启生效

# 查看效果
free -m

# 重新启动swap分区
swapon -a
```
### 2.5 网桥过滤
```bash
cat > /etc/sysctl.d/k8s.conf << EOF 
net.bridge.bridge-nf-call-ip6tables = 1
net.bridge.bridge-nf-call-iptables = 1
net.bridge.bridge-nf-call-arptables = 1 
net.ipv4.ip_forward=1 
net.ipv4.ip_forward_use_pmtu = 0 
EOF


# 生效命令 
sysctl --system

```

### 2.6 时间同步
```bash
# 安装软件
yum -y install ntpdate

# 向阿里云服务器同步时间
ntpdate time1.aliyun.com

# 删除本地时间并设置时区为上海
rm -rf /etc/localtime 
ln -s /usr/share/zoneinfo/Asia/Shanghai /etc/localtime

# 查看时间 
date -R || date
```

## 3. 所有节点安装Docker
```bash
1.卸载旧版本
yum remove docker \
docker-client \
docker-client-latest \
docker-common \
docker-latest \
docker-latest-logrotate \
docker-logrotate \
docker-engine
		
2.安装需要的依赖包		
yum install -y yum-utils

3.设置阿里云docker镜像
yum-config-manager --add-repo http://mirrors.aliyun.com/docker-ce/linux/centos/docker-ce.repo && yum makecache

4.安装docker
yum -y install docker-ce docker-ce-cli containerd.io

5.启动Docker 
systemctl start docker && systemctl enable docker && systemctl status docker

6.查看docker版本信息 
docker info
```

## 4. 所有节点配置阿里云Docker、kubernetes镜像
```bash
1. 配置阿里云docker镜像加速
mkdir -p /etc/docker
cat > /etc/docker/daemon.json << EOF 
{ 
  "registry-mirrors": ["https://l6p4ic76.mirror.aliyuncs.com"],
  "log-driver":"json-file",
  "exec-opts": ["native.cgroupdriver=systemd"],
  "log-opts": {"max-size":"500m", "max-file":"3"}
}
EOF
systemctl restart docker

2. 配置阿里云Kubernetes 镜像
cat >> /etc/yum.repos.d/kubernetes.repo << EOF
[kubernetes]
name=Kubernetes
baseurl=https://mirrors.aliyun.com/kubernetes/yum/repos/kubernetes-el7-x86_64/
enabled=1
gpgcheck=1
repo_gpgcheck=1
gpgkey=https://mirrors.aliyun.com/kubernetes/yum/doc/yum-key.gpg https://mirrors.aliyun.com/kubernetes/yum/doc/rpm-package-key.gpg
EOF
```

## 5. 所有节点安装kubelet kubeadm kubectl
```bash
yum install -y --nogpgcheck kubelet-1.22.15 kubeadm-1.22.15 kubectl-1.22.15

# 指定K8S版本安装，不指定版本默认安装最新版。 
# yum install -y --nogpgcheck kubelet kubeadm kubectl
systemctl enable kubelet && systemctl start kubelet
```

## 6. 部署Kubernetes Master节点
**`这里指定阿里云镜像仓库地址，默认的镜像地址无法加载访问。`**
```bash
kubeadm init \
--apiserver-advertise-address=192.168.211.201 \
--image-repository registry.aliyuncs.com/google_containers \
--kubernetes-version v1.22.15 \
--service-cidr=10.96.0.0/12 \
--pod-network-cidr=10.244.0.0/16
```

> 若出现错误
```bash
[root@almalinux ~]# kubeadm init \
>   --apiserver-advertise-address=192.168.211.201 \
>   --image-repository registry.aliyuncs.com/google_containers \
>   --kubernetes-version v1.25.3 \
>   --service-cidr=10.96.0.0/12 \
>   --pod-network-cidr=10.244.0.0/16
[init] Using Kubernetes version: v1.25.3
[preflight] Running pre-flight checks
        [WARNING FileExisting-tc]: tc not found in system path
error execution phase preflight: [preflight] Some fatal errors occurred:
        [ERROR CRI]: container runtime is not running: output: E1029 14:48:00.390255   29768 remote_runtime.go:948] "Status from runtime service failed" err="rpc error: code = Unimplemented desc = unknown service runtime.v1alpha2.RuntimeService"
time="2022-10-29T14:48:00+08:00" level=fatal msg="getting status of runtime: rpc error: code = Unimplemented desc = unknown service runtime.v1alpha2.RuntimeService"
, error: exit status 1
[preflight] If you know what you are doing, you can make a check non-fatal with `--ignore-preflight-errors=...`
To see the stack trace of this error execute with --v=5 or higher
```
> 解决办法
```bash
rm -rf /etc/containerd/config.toml
systemctl restart containerd

# 最后使用
kubeadm reset
```
> 若出现错误
```bash
[root@k8s-master ~]# kubeadm init \
> --apiserver-advertise-address=192.168.211.201 \
> --image-repository registry.aliyuncs.com/google_containers \
> --kubernetes-version v1.25.3 \
> --service-cidr=10.96.0.0/12 \
> --pod-network-cidr=10.244.0.0/16
[init] Using Kubernetes version: v1.25.3
[preflight] Running pre-flight checks
        [WARNING FileExisting-tc]: tc not found in system path
        [WARNING Hostname]: hostname "k8s-master" could not be reached
        [WARNING Hostname]: hostname "k8s-master": lookup k8s-master on 223.5.5.5:53: no such host
error execution phase preflight: [preflight] Some fatal errors occurred:
        [ERROR FileContent--proc-sys-net-bridge-bridge-nf-call-iptables]: /proc/sys/net/bridge/bridge-nf-call-iptables does not exist
[preflight] If you know what you are doing, you can make a check non-fatal with `--ignore-preflight-errors=...`
To see the stack trace of this error execute with --v=5 or higher
```
> 解决方法

```bash
modprobe br_netfilter
echo 1 > /proc/sys/net/bridge/bridge-nf-call-iptables
```

> **注意：** 如果要使用 `kubectl get nodes` 命令需要做以下配置

```bash
 - master节点，root用户，执行以下命令
echo "export KUBECONFIG=/etc/kubernetes/admin.conf" >> ~/.bash_profile
source ~/.bash_profile

 - master节点，非root用户，执行以下命令
mkdir -p $HOME/.kube
sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
sudo chown $(id -u):$(id -g) $HOME/.kube/config

- node节点需要从 master 节点拷贝 admin.con 文件到 node 节点上
scp root@master:/etc/kubernetes/admin.conf /etc/kubernetes/
- root 和 非root 用户的命令同master

```


## 7. 部署网络插件
```bash
# 以下网络插件任选一个
# CNI网络插件
kubectl apply -f https://raw.githubusercontent.com/coreos/flannel/master/Documentation/kube-flannel.yml

# Flannel网络插件
kubectl apply -f https://kuboard.cn/install-script/flannel/flannel-v0.14.0.yaml

# 查看
kubectl get pods -n kube-system
```
> 若发现有`Pending`的删除即可，会自动重新部署

```bash
[root@k8s-master ~]# kubectl get pods -n kube-system
NAME                                 READY   STATUS    RESTARTS   AGE
coredns-7f6cbbb7b8-6gxf6             0/1     Pending   0          23m
coredns-7f6cbbb7b8-nnjsk             0/1     Pending   0          23m
etcd-k8s-master                      1/1     Running   1          23m
kube-apiserver-k8s-master            1/1     Running   1          23m
kube-controller-manager-k8s-master   1/1     Running   1          23m
kube-proxy-6d4d6                     1/1     Running   0          18m
kube-proxy-m4vx9                     1/1     Running   0          23m
kube-scheduler-k8s-master            1/1     Running   1          23m
[root@k8s-master ~]# kubectl delete pods coredns-7f6cbbb7b8-6gxf6 coredns-7f6cbbb7b8-nnjsk  -n kube-system
pod "coredns-7f6cbbb7b8-6gxf6" deleted
pod "coredns-7f6cbbb7b8-nnjsk" deleted
[root@k8s-master ~]# kubectl get pods -n kube-system
NAME                                 READY   STATUS    RESTARTS   AGE
coredns-7f6cbbb7b8-4df47             1/1     Running   0          11s
coredns-7f6cbbb7b8-wxzcl             1/1     Running   0          11s
etcd-k8s-master                      1/1     Running   1          24m
kube-apiserver-k8s-master            1/1     Running   1          24m
kube-controller-manager-k8s-master   1/1     Running   1          24m
kube-proxy-6d4d6                     1/1     Running   0          19m
kube-proxy-m4vx9                     1/1     Running   0          24m
kube-scheduler-k8s-master 
```

## 8. 部署node节点
```bash
# 只在 master 节点执行
kubeadm token create --print-join-command

# 在node节点中执行打印出的结果
kubeadm join 192.168.211.201:6443 --token hfyeoe.ie453hoen4eku70w --discovery-token-ca-cert-hash sha256:3716cd7f3c8a52b78b1ab495e7fbd3c6f7dabd899a0237c203c05bce11ac9be6

# 在 master 节点中查看
[root@k8s-master ~]# kubectl get nodes
NAME         STATUS   ROLES                  AGE     VERSION
k8s-master   Ready    control-plane,master   59m     v1.22.3
k8s-node1    Ready    <none>                 54m     v1.22.3
k8s-node2    Ready    <none>                 3m53s   v1.22.3
```
> **注意：** 若新加入的node节点出现`NotReady`等待一会即可

## 9. 添加图形化管理（选做）
```bash
# 在 master 节点执行
kubectl apply -f https://addons.kuboard.cn/kuboard/kuboard-v3-swr.yaml
```

执行指令 `watch kubectl get pods -n kuboard`，等待 kuboard 名称空间中所有的 Pod 就绪
```bash
root@k8s-master ~]# kubectl get pods -n kuboard
NAME                               READY   STATUS    RESTARTS   AGE
kuboard-agent-2-85d76b44dd-jvpm2   1/1     Running   0          9s
kuboard-agent-67864c5f66-4w9z2     1/1     Running   0          9s
kuboard-etcd-htppb                 1/1     Running   0          36s
kuboard-v3-765f7bcbfd-lpwct        0/1     Running   0          36s
```

### 访问 Kuboard

-   在浏览器中打开链接 `http://your-node-ip-address:30080`
    
-   输入初始用户名和密码，并登录
    
    -   用户名： `admin`
    -   密码： `Kuboard123`

### 卸载

```bash
kubectl delete -f https://addons.kuboard.cn/kuboard/kuboard-v3-swr.yaml
rm -rf /usr/share/kuboard
```