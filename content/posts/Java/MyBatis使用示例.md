---
title: "MyBatis使用示例"
description: "MyBatis使用示例"
keywords: "Java"

date: 2023-06-16T16:41:30+08:00
lastmod: 2023-06-16T16:41:30+08:00

categories:
  - Java
tags:
  - Java
  - MyBatis
---

## 什么是 MyBatis？

MyBatis 是一款优秀的持久层框架，它支持自定义 SQL、存储过程以及高级映射。MyBatis 免除了几乎所有的 JDBC 代码以及设置参数和获取结果集的工作。MyBatis 可以通过简单的 XML 或注解来配置和映射原始类型、接口和 Java POJO（Plain Old Java Objects，普通老式 Java 对象）为数据库中的记录。

## 创建MyBatis项目

使用 IDEA 建立一个 SpringBoot 项目，初始化组件部分选择 Web、JDBC API、MyBatis Framework、MySQL Driver

若创建失败或者创建太慢，可以更换阿里镜像：[https://start.aliyun.com](https://start.aliyun.com)

![](https://picture-czy.oss-cn-beijing.aliyuncs.com/homeWinDir/20210731195058.png#crop=0&crop=0&crop=1&crop=1&id=swlw5&originHeight=778&originWidth=850&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

![](https://picture-czy.oss-cn-beijing.aliyuncs.com/homeWinDir/20210731195345.png#crop=0&crop=0&crop=1&crop=1&id=gofR3&originHeight=302&originWidth=396&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

## 创建数据库

```sql
CREATE DATABASE IF NOT EXISTS mybatis;
USE mybatis;
```

其对应的数据库 Schema 脚本如下：

```sql
DROP TABLE IF EXISTS user;

CREATE TABLE user
(
	id BIGINT(20) NOT NULL COMMENT '主键ID',
	name VARCHAR(30) NULL DEFAULT NULL COMMENT '姓名',
	age INT(11) NULL DEFAULT NULL COMMENT '年龄',
	email VARCHAR(50) NULL DEFAULT NULL COMMENT '邮箱',
	PRIMARY KEY (id)
);
```

其对应的数据库 Data 脚本如下：

```sql
INSERT INTO user (id, name, age, email) VALUES
(1, 'Jone', 18, 'test1@baomidou.com'),
(2, 'Jack', 20, 'test2@baomidou.com'),
(3, 'Tom', 28, 'test3@baomidou.com'),
(4, 'Sandy', 21, 'test4@baomidou.com'),
(5, 'Billie', 24, 'test5@baomidou.com');
```

## 创建对应的程序目录和类

- controller层负责具体的业务模块流程的控制
- dao层主要是做数据持久层的工作，负责与数据库联络，封装了增删改查基本操作
- entity层用于存放我们的实体类，与数据库中的属性值基本保持一致，实现set和get的方法
- service层主要负责业务模块的逻辑应用设计，具体要调用到已定义的DAO层的接口

![](https://picture-czy.oss-cn-beijing.aliyuncs.com/homeWinDir/20210731200633.png#crop=0&crop=0&crop=1&crop=1&id=fNBTg&originHeight=323&originWidth=471&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

```java
package com.colzry.mybatis.entity;


import lombok.Data;

@Data
public class User {
    private Integer id;
    private String name;
    private Integer age;
    private String email;
}
```

```java
package com.colzry.mybatis.dao;

import com.colzry.mybatis.entity.User;
import org.apache.ibatis.annotations.Mapper;
import org.apache.ibatis.annotations.Param;
import org.apache.ibatis.annotations.Select;
import org.springframework.stereotype.Repository;

import java.util.List;

@Mapper
@Repository
public interface UserDao {

    @Select("select * from user where id=#{id}")
    public User getUserById(@Param("id") Integer id);

    @Select("select * from user where age >= #{age}")
    public List<User> getUserByGtAge(@Param("age") int age);
}
```

```java
package com.colzry.mybatis.service;

import com.colzry.mybatis.dao.UserDao;
import com.colzry.mybatis.entity.User;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Service;

import java.util.List;

@Service
public class UserService {

    @Autowired
    private UserDao userDao;

    public User queryUser(int id) {
        return userDao.getUserById(id);
    }

    public List<User> queryUser01(int age) {
        return userDao.getUserByGtAge(age);
    }

}
```

```java
package com.colzry.mybatis.controller;

import com.colzry.mybatis.entity.User;
import com.colzry.mybatis.service.UserService;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RequestParam;
import org.springframework.web.bind.annotation.RestController;

import java.util.List;

@RestController
public class UserController {

    @Autowired
    private UserService userService;

    @RequestMapping("/user")
    public User getUser(@RequestParam("id") int id) {
        return userService.queryUser(id);
    }

    @RequestMapping("/user01")
    public List<User> getUserByAge(@RequestParam("age") int age) {
        return userService.queryUser01(age);
    }
}
```

## 编写配置文件

对应的`application.yml`文件

```yaml
server:
  port: 9090

spring:
  datasource:
    driver-class-name: com.mysql.cj.jdbc.Driver
    url: jdbc:mysql://localhost:3306/mybatis?serverTimezone=UTC
    username: mybatis
    password: 123456

mybatis:
  mapper-locations: classpath:/mappers/*.xml
  type-aliases-package: com.colzry.mybatis.entity
```

## 运行项目

运行`MyBatisApplication`主程序

在浏览器中访问本地的9090端口，输入以下两个内容进行测试

```shell
http://localhost:9090/user?id=1
http://localhost:9090/user01?age=18
```

![](https://picture-czy.oss-cn-beijing.aliyuncs.com/homeWinDir/20210731201803.png#crop=0&crop=0&crop=1&crop=1&id=ulBnC&originHeight=286&originWidth=762&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

![](https://picture-czy.oss-cn-beijing.aliyuncs.com/homeWinDir/20210731201742.png#crop=0&crop=0&crop=1&crop=1&id=lb5e7&originHeight=768&originWidth=758&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)
