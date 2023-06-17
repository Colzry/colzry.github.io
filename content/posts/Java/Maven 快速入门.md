---
title: "Maven快速入门"
description: "Maven快速入门"
keywords: "Java"

date: 2023-06-16T16:41:30+08:00
lastmod: 2023-06-16T16:41:30+08:00

categories:
  - Java
tags:
  - Java
  - Maven
---

## Maven的作用

1.  项目的自动构建，帮助开发人员做项目代码的编译，测试， 打包，安装，部署等工作。 
2.  管理依赖（管理项目中使用的各种jar包）。 
   - 依赖：项目中需要使用的其他资源，  常见的是jar 。 比如项目要使用`mysql`驱动。我们就说项目依赖`mysql`驱动。

## Maven 安装

1.  确定`JAVA_HOME` 指定jdk的安装目录， 如果没有`JAVA_HOME`， 需要在windows的环境变量中创建`JAVA_HOME`, 它的值是jdk的安装目录 
2.  解压缩  apache-maven-3.3.9-bin.zip ，把解压后的文件放到一个目录中。
目录的路径不要有中文，不要有空格。 
3.  把maven安装目录中下的bin的路径添加到path中 
4.  测试maven的安装。 在命令行执行 mvn -v 
```shell
C:\Users\NING MEI>mvn -v
Apache Maven 3.3.9 (bb52d8502b132ec0a5a3f4c09453c07478323dc5; 2015-11-11T00:41:47+08:00)
Maven home: D:\tools\apache-maven-3.3.9\bin\..
Java version: 1.8.0_101, vendor: Oracle Corporation
Java home: C:\Program Files\Java\jdk1.8.0_101\jre
Default locale: zh_CN, platform encoding: GBK
OS name: "windows 10", version: "10.0", arch: "amd64", family: "dos"
```

maven解压后的目录结构
![](https://picture-czy.oss-cn-beijing.aliyuncs.com/img/20210721191802.png#crop=0&crop=0&crop=1&crop=1&id=dVqiI&originHeight=285&originWidth=498&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)
maven的其他安装方式： 

   1.  确定`JAVA_HOME`是否有效 
   2.  在环境变量中，创建一个叫做`M2_HOME` (或者`MAVEN_HOME`) ，它的值是maven的安装目录
M2_HOME=D:\tools\apache-maven-3.3.9 
   3.  在path环境变量中，加入 %M2_HOME%\bin 
   4.  测试maven的安装，在命令行执行 mvn  -v 
```shell
C:\Users\NING MEI>mvn -v
Apache Maven 3.3.9 (bb52d8502b132ec0a5a3f4c09453c07478323dc5; 2015-11-11T00:41:47+08:00)
Maven home: D:\tools\apache-maven-3.3.9\bin\..
Java version: 1.8.0_101, vendor: Oracle Corporation
Java home: C:\Program Files\Java\jdk1.8.0_101\jre
Default locale: zh_CN, platform encoding: GBK
OS name: "windows 10", version: "10.0", arch: "amd64", family: "dos"
```


## Maven的核心概念

### 目录结构

一个maven项目是一个文件夹。 比如项目叫做Hello

```java
Hello 项目文件夹
 \src
 	\main				叫做主程序目录（完成项目功能的代码和配置文件）
          \java          源代码（包和相关的类定义）
 		 \resources	    配置文件
 	\test               放置测试程序代码的（开发人员自己写的测试代码）
 		 \java          测试代码的（junit）
 		 \resources     测试程序需要的配置文件
 \pom.xml                maven的配置文件， 核心文件
```

### POM

POM： Project Object Model 项目对象模型， maven把项目当做模型处理。 操作这个模型就是操作项目。

maven通过`pom.xml`文件实现 项目的构建和依赖的管理。

```xml
<?xml version="1.0" encoding="UTF-8"?>

<!-- project是根标签， 后面的是约束文件 -->
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
 
 
<!-- pom模型的版本， 就是4.0.0 -->  
<modelVersion>4.0.0</modelVersion>

<!-- 坐标 -->  
<groupId>com.bjpowernode</groupId>
<artifactId>ch01-maven</artifactId>
<version>1.0-SNAPSHOT</version>

<properties>
  <java.version>1.8</java.version>
  <maven.compiler.source>1.8</maven.compiler.source>
  <maven.compiler.target>1.8</maven.compiler.target>
</properties>

</project>
```

### 坐标

坐标组成是 `groupid`, `artifiactId`, `version`。

**坐标作用：确定资源的，是资源的唯一标识。** 在`maven`中，每个资源都是坐标。 坐标值是唯一的。简称叫`gav`

```xml
<groupId>com.bjpowernode</groupId>
<artifactId>ch01-maven</artifactId>
<version>1.0-SNAPSHOT</version>
<packaging>jar</packaging>

groupId: 组织名称，代码。 公司，团体或者单位的标识。 这个值常使用的公司域名的倒写。
      例如：学校的网站 www.bjpowernode.com, groupId: com.bjpowernode

      如果项目规模比较大， 也可以是 域名倒写+大项目名称。
      例如： www.baidu.com ,  无人车： com.baidu.appollo
artifactId:项目名称， 如果groupId中有项目， 此时当前的值就是子项目名。 项目名称是唯一的。
version：版本， 项目的版本号， 使用的数字。 三位组成。 例如 主版本号.次版本号.小版本号， 例如： 5.2.5。
      注意：版本号中有-SNAPSHOT， 表示快照，不是稳定的版本。      

packaging 项目打包的类型， 有jar ，war， ear， pom等等 默认是jar
```

### 依赖 dependency

依赖：项目中要使用的其他资源（jar）。

需要使用maven表示依赖，管理依赖。 通过使用dependency和gav一起完成依赖的使用

需要在pom.xml文件中，使用dependencies 和dependency， 还有gav 完成依赖的说明。

格式：

```xml
<dependencies>
  
    <!-- 日志 -->
    <dependency>
        <groupId>log4j</groupId>
        <artifactId>log4j</artifactId>
        <version>1.2.17</version>
    </dependency>
    
    <!-- mysql驱动 -->
     <dependency>
        <groupId>mysql</groupId>
        <artifactId>mysql-connector-java</artifactId>
        <version>5.1.16</version>
    </dependency>

</dependencies> 

maven使用gav作为标识，从互联网下载依赖的jar。 下载到你的本机上。  由maven管理项目使用的这些jar
```

搜索依赖的地址： [https://mvnrepository.com/](https://mvnrepository.com/)

### 仓库

仓库是存东西的，maven的仓库存放的是：

1.  maven工具自己的jar包。 
2.  第三方的其他jar， 比如项目中要使用mysql驱动。 
3.  自己写的程序，可以打包为jar 。 存放到仓库。 

仓库的分类：

1.  本地仓库：默认路径，是你登录操作系统的账号的目录中`%HOMEPATH%/.m2/repository` 
```xml
     C:\Users\NING MEI\.m2\repository
```

修改本地仓库的位置：修改maven工具的配置文件（maven的安装路径\conf\setting.xml）
	步骤：
     1）创建一个目录，作为仓库使用。  目录不要有中文和空格。 目录不要太深。
		   例如： D:\openrepository
     2）修改setting.xml文件，指定 D:\openrepository这个目录  

2.  远程仓库： 需要通过联网访问的
1）中央仓库： 一个ftp服务器， 存放了所有的资源。
2）中央仓库的镜像： 就是中央仓库的拷贝。 在各大主要城市都有镜像。
3）私服：在局域网中使用的。 私服就是自己的仓库服务器。 在公司内部使用的。 

maven使用仓库： maven自动使用仓库， 当项目启动后， 执行了maven的命令， maven首先访问的是本地仓库， 从仓库中获取所需的jar， 如果本地仓库没有 ，需要访问私服或者中央仓库或者镜像。

### maven的生命周期

maven的生命周期： 项目构建的各个阶段。 包括 清理， 编译， 测试，报告，打包，安装，部署

#### 命令

```
mvn clean		清理命令
mvn compile		编译命令
mvn test		测试命令
mvn package		打包
mvn install  	把生成的打包的文件 ，安装到maven仓库。
mvn deploy		部署
```

## IDEA的使用

在IDEA中创建`Maven`项目

![](https://picture-czy.oss-cn-beijing.aliyuncs.com/img/20210721202010.png#crop=0&crop=0&crop=1&crop=1&id=TGgoi&originHeight=1030&originWidth=1014&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

![](https://picture-czy.oss-cn-beijing.aliyuncs.com/img/20210721202250.png#crop=0&crop=0&crop=1&crop=1&id=cRc2T&originHeight=1030&originWidth=1014&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

![](https://picture-czy.oss-cn-beijing.aliyuncs.com/img/20210721203238.png#crop=0&crop=0&crop=1&crop=1&id=ECMQW&originHeight=1042&originWidth=1920&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

#### pom.xml的配置

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <groupId>org.example</groupId>
    <artifactId>MavenDemo</artifactId>
    <version>1.0-SNAPSHOT</version>

    <properties>
        <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
        <maven.compiler.source>14</maven.compiler.source>
        <maven.compiler.target>14</maven.compiler.target>
        <!--项目构建使用的编码，避免中文乱码-->
        <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
        <!--生成报告的编码-->
        <project.reporting.outputEncoding>UTF-8</project.reporting.outputEncoding>
        <!--自定义Spring版本变量-->
        <spring.version>5.2.12.RELEASE</spring.version>
    </properties>

    <dependencies>

        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-context</artifactId>
            <version>${spring.version}</version>
        </dependency>

        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-core</artifactId>
            <version>${spring.version}</version>
        </dependency>

        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-beans</artifactId>
            <version>${spring.version}</version>
        </dependency>

        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring</artifactId>
            <version>${spring.version}</version>
            <type>pom</type>
        </dependency>

        <dependency>
            <groupId>junit</groupId>
            <artifactId>junit</artifactId>
            <version>4.13.1</version>
            <scope>compile</scope>
        </dependency>

    </dependencies>
</project>
```

### 更改IDEA中的Maven成本地安装的Maven

![](https://picture-czy.oss-cn-beijing.aliyuncs.com/img/20210721204127.png#crop=0&crop=0&crop=1&crop=1&id=KNAJI&originHeight=1034&originWidth=1521&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

### Maven 配置阿里镜像

在`Maven`的安装目录的conf下修改settings.xml文件，添加下面的内容

```xml
<mirrors>
      <mirror>
        <id>nexus-aliyun</id>
        <mirrorOf>central</mirrorOf>
        <name>Nexus aliyun</name>
        <url>http://maven.aliyun.com/nexus/content/groups/public</url>
      </mirror>

	<mirror> 
		<id>huaweicloud</id> 
		<mirrorOf>central</mirrorOf> 
		<name>Nexus huaweicloud</name>
		<url>https://repo.huaweicloud.com/repository/maven/</url> 
	</mirror>

	<mirror> 
		<id>nexus-tencentyun</id> 
		<mirrorOf>central</mirrorOf> 
		<name>Nexus tencentyun</name> 
		<url>http://mirrors.cloud.tencent.com/nexus/repository/maven-public/</url> 
	</mirror>
	
  </mirrors>
 
  <profiles>
         <profile>
              <id>jdk-1.8</id>
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
  </profiles>
```

### 修改UTF-8为默认编码

设置环境变量

变量名 MAVEN_OPTS

变量值 -Xms256m -Xmx512m -Dfile.encoding=UTF-8


## Linux安装Maven
```bash
wget https://repo.huaweicloud.com/apache/maven/maven-3/3.9.0/binaries/apache-maven-3.9.0-bin.tar.gz

tar -zxvf apache-maven-3.9.0-bin.tar.gz -C /usr/local/

cat > /etc/profile.d/my_env.sh <<-EOF
export M2_HOME=/usr/local/apache-maven-3.9.0
export PATH=$M2_HOME/bin:$PATH
EOF

source /etc/profile
```