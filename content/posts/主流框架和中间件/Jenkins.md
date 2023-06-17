---
title: "JenKins入门"
description: "Jenkins入门"
keywords: "Jenkins"

date: 2023-06-16T16:41:30+08:00
lastmod: 2023-06-16T16:41:30+08:00

categories:
  - 框架和中间件
tags:
  - Jenkins
---

## 1. 安装准备
### 1.1 安装Docker
```shell
$ curl -fsSL get.docker.com -o get-docker.sh
$ sudo sh get-docker.sh --mirror Aliyun

$ sudo systemctl start docker
$ sudo systemctl enable docker

```
### 1.2 安装docker-compose
```bash
$ dnf install docker-compose -y
```
### 1.3 安装gitlab
拉取gitlab镜像
```bash
docker pull gitlab/gitlab-ce
```
建立compose文件目录
```bash
mkdir -p /usr/local/docker/gitlab_docker
```
创建compose文件
```bash
cd /usr/local/docker/gitlab_docker
vim docker-compose.yml

version: '3.0'
services:
  gitlab:
    image: 'gitlab/gitlab-ce'
    container_name: gitlab
    restart: always
    environment:
      TZ: 'Asia/Shanghai'
      GITLAB OMNIBUS CONFIG:
        external_url 'http://192.168.211.100:8929'
        gitlab_rails['gitlab_shell_ssh_port'] = 2224
    ports:
      - '8929:8929'
      - '2224:2224'
    volumes:
      - './config:/etc/gitlab'
      - './logs:/var/log/gitlab'
      - './data:/var/opt/gitlab'
```
启动
```bash
docker-compose up -d
# 查看日志
docker-compose logs -f


# 查看root账户密码
docker exec -it gitlab bash

```
### 1.4 安装JDK8
```bash
bash <(curl -Ssl https://picture-czy.oss-cn-beijing.aliyuncs.com/shareFile/jdk-install.sh)
```
### 1.5 安装Maven
下载地址：

```bash
wget https://mirrors.tuna.tsinghua.edu.cn/apache/maven/maven-3/3.8.6/binaries/apache-maven-3.8.6-bin.tar.gz

tar zxvf apache-maven-3.8.6-bin.tar.gz -C /usr/local
echo "export MAVEN_HOME=/usr/local/apache-maven-3.8.6" >> ~/.bashrc
echo "export PATH=$MAVEN_HOME/bin:$PATH" >> ~/.bashrc
source ~/.bashrc
mvn -version
```
修改配置文件
```bash
vim /usr/local/apache-maven-3.8.6/conf/settings.xml
```
```xml
<mirror>
  <id>alimaven</id>
  <name>aliyun maven</name>
  <url>http://maven.aliyun.com/nexus/content/groups/public/</url>
  <mirrorOf>central</mirrorOf>
</mirror>

<profile>
  <id>jdk8</id>
  <activation>
    <activeByDefault>true</activeByDefault>
    <jdk>1.8</jdk>
  </activation>
  <properties>
    <maven.compiler.source>1.8</maven.compiler.source>
    <maven.compiler.target>1.8</maven.compiler.target>
    <maven.compiler.compilerVersion>1.8</maven.compiler.compilerVersion>
  </properties>
</profile>
```
### 1.6 安装Jenkins
#### 1.6.1 通过docker安装jenkins
docker安装地址：
[https://hub.docker.com/r/jenkins/jenkins/tags?page=1&name=lts](https://hub.docker.com/r/jenkins/jenkins/tags?page=1&name=lts)
```bash
docker pull jenkins/jenkins:2.361.1-lts

cd /usr/local/
mkdir -p docker/jenkins_docker
cd docker/jenkins_docker
vim docker-compose.yml

################################################
version: "3.1"
services:
  jenkins:
    image: jenkins/jenkins:2.361.1-lts
    container_name: jenkins
    ports:
      - 8080:8080
      - 50000:50000
    volumes:
      - ./data/:/var/jenkins_home/
################################################

mkdir data
chmod -R 777 data/
docker-compose up -d

# 通过日志查看密码
docker logs -f jenkins
```
进入8080端口进行登录，密码可以在日志中查看
进入后选择右边的默认安装，之后都是下一步
#### 1.6.2 安装Jenkins插件
![image.png](https://cdn.nlark.com/yuque/0/2022/png/22223333/1663068844930-22110b8f-e836-4381-b522-6fb8d947aab8.png#clientId=u58e717ac-615a-4&crop=0&crop=0&crop=1&crop=1&from=paste&height=603&id=u9a3a76b2&margin=%5Bobject%20Object%5D&name=image.png&originHeight=904&originWidth=2232&originalType=binary&ratio=1&rotation=0&showTitle=false&size=187185&status=done&style=none&taskId=u417590b5-d75a-4876-affd-dde75e94cef&title=&width=1488)
![image.png](https://cdn.nlark.com/yuque/0/2022/png/22223333/1663069054805-a2abb28f-70a6-4de7-b9c7-8ce8e86993cf.png#clientId=u58e717ac-615a-4&crop=0&crop=0&crop=1&crop=1&from=paste&height=628&id=u4ab9a670&margin=%5Bobject%20Object%5D&name=image.png&originHeight=942&originWidth=2240&originalType=binary&ratio=1&rotation=0&showTitle=false&size=103293&status=done&style=none&taskId=ua7972d39-a489-402a-ad20-09e195920d5&title=&width=1493.3333333333333)
#### 1.6.3 CP JDK和Maven
```bash
cd /usr/local/docker/jenkins_docker/data/
cp -r /usr/local/jdk1.8.0_202/ ./
cp -r /usr/local/apache-maven-3.8.6/ ./
```
![image.png](https://cdn.nlark.com/yuque/0/2022/png/22223333/1663069865794-0ceaf929-517c-4569-bca0-d67657390bb5.png#clientId=u58e717ac-615a-4&crop=0&crop=0&crop=1&crop=1&from=paste&height=621&id=u7c4498d8&margin=%5Bobject%20Object%5D&name=image.png&originHeight=932&originWidth=2196&originalType=binary&ratio=1&rotation=0&showTitle=false&size=66261&status=done&style=none&taskId=u14880aee-7bc5-46e6-96a8-7d7c7b216ed&title=&width=1464)![image.png](https://cdn.nlark.com/yuque/0/2022/png/22223333/1663069888991-ce318309-b721-46cd-ab31-60a5c2036471.png#clientId=u58e717ac-615a-4&crop=0&crop=0&crop=1&crop=1&from=paste&height=385&id=uc534b282&margin=%5Bobject%20Object%5D&name=image.png&originHeight=578&originWidth=1526&originalType=binary&ratio=1&rotation=0&showTitle=false&size=25718&status=done&style=none&taskId=ubc0d0256-69c7-47d4-b9aa-d08be41445b&title=&width=1017.3333333333334)![image.png](https://cdn.nlark.com/yuque/0/2022/png/22223333/1663069993728-ec808aca-3292-40d7-96ae-6f5507042c36.png#clientId=u58e717ac-615a-4&crop=0&crop=0&crop=1&crop=1&from=paste&height=528&id=u866aaa55&margin=%5Bobject%20Object%5D&name=image.png&originHeight=792&originWidth=1530&originalType=binary&ratio=1&rotation=0&showTitle=false&size=41569&status=done&style=none&taskId=u6bcbe76f-6051-4b47-b062-12078b8558a&title=&width=1020)![image.png](https://cdn.nlark.com/yuque/0/2022/png/22223333/1663070618977-21370313-d0df-4a14-9a35-f7bf14019a63.png#clientId=u58e717ac-615a-4&crop=0&crop=0&crop=1&crop=1&from=paste&height=766&id=uc7b8815d&margin=%5Bobject%20Object%5D&name=image.png&originHeight=1149&originWidth=2169&originalType=binary&ratio=1&rotation=0&showTitle=false&size=71271&status=done&style=none&taskId=u84626d82-6c36-4627-8501-aead0730214&title=&width=1446)
