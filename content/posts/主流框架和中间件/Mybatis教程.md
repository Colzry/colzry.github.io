---
title: "MyBatis教程"
description: "MyBatis教程"
keywords: "MyBatis"

date: 2023-06-16T16:41:30+08:00
lastmod: 2023-06-16T16:41:30+08:00

categories:
  - 框架和中间件
tags:
  - MyBatis
---

# 码神之路之Mybatis教程

ORM是什么？

为什么要将JDBC封装为Mybatis？

怎么使用Mybatis实现增删改查？

sql注入？

标签，标签，标签，标签等标签的使用?

Mybatis如何实现分页?

Mybatis分页插件的使用?

Mybatis的原理?

# 目录

1. JDBC实现过程
2. JDBC的问题
3. JDBC演变为Mybatis
4. Mybatis的使用
5. Mybatis的原理
6. 后续讲解

   1. 在spring阶段讲解和Mybatis的整合
   2. 在springboot阶段 讲解和Mybatis的整合
   3. Mybatis-plus的使用

## 1. JDBC实现过程

### 1.1 步骤

我们最熟悉也是最基础的通过JDBC查询数据库数据，一般需要以下七个步骤：

1. 加载JDBC驱动；
2. 建立并获取数据库连接；
3. 创建 JDBC Statements 对象；
4. 设置SQL语句的传入参数；
5. 执行SQL语句并获得查询结果；
6. 对查询结果进行转换处理并将处理结果返回；
7. 释放相关资源（关闭Connection，关闭Statement，关闭ResultSet）；

### 1.2 代码

#### 1.2.1 新建maven工程

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <groupId>com.xiaopizhu</groupId>
    <artifactId>mybatisDemo</artifactId>
    <version>1.0-SNAPSHOT</version>


    <dependencies>
        <!-- mybatis -->
        <dependency>
            <groupId>org.mybatis</groupId>
            <artifactId>mybatis</artifactId>
            <version>3.5.6</version>
        </dependency>
        <dependency>
            <groupId>mysql</groupId>
            <artifactId>mysql-connector-java</artifactId>
            <version>8.0.23</version>
        </dependency>
        <!--junit -->
        <dependency>
            <groupId>junit</groupId>
            <artifactId>junit</artifactId>
            <version>4.12</version>
            <scope>test</scope>
        </dependency>
        <!-- lombok -->
        <dependency>
            <groupId>org.projectlombok</groupId>
            <artifactId>lombok</artifactId>
            <version>1.18.16</version>
            <scope>provided</scope>
        </dependency>
        
    </dependencies>
    
     <build>
        <plugins>
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-compiler-plugin</artifactId>
                <version>3.8.0</version>
                <configuration>
                    <target>8</target>
                    <source>8</source>
                </configuration>
            </plugin>
        </plugins>
    </build>
    
</project>
```

#### 1.2.2 新建UserJDBCDao.java

```java
package com.xiaopizhu.mybatis.dao;

import com.xiaopizhu.mybatis.pojo.User;

import java.sql.*;
import java.util.ArrayList;
import java.util.Collections;
import java.util.List;

public class UserJDBCDao {

    public List<User> findUserListGtAge(int age){
        String url = "jdbc:mysql://localhost:3306/god?serverTimezone=UTC&characterEncoding=utf-8";
        String name = "root";
        String password = "root";
        Connection connection = null;
        PreparedStatement preparedStatement = null;
        ResultSet resultSet = null;
        try {
            Class.forName("com.mysql.cj.jdbc.Driver");
            connection = DriverManager.getConnection(url,name,password);
            String sql = "select * from g_user where age > ?";
            preparedStatement = connection.prepareStatement(sql);
            preparedStatement.setInt(1,age);
            resultSet = preparedStatement.executeQuery();
            List<User> userList = new ArrayList<>();
            while (resultSet.next()){
                long id = resultSet.getLong("id");
                String username = resultSet.getString("username");
                int age1 = resultSet.getInt("age");
                User user = new User();
                user.setId(id);
                user.setUsername(username);
                user.setAge(age1);
                userList.add(user);
            }
            return userList;
        } catch (Exception e) {
            e.printStackTrace();
            return Collections.emptyList();
        }finally {
            try {
                if (resultSet != null){
                    resultSet.close();
                }
                if (preparedStatement != null){
                    preparedStatement.close();
                }
                if (connection != null){
                    connection.close();
                }
            } catch (Exception e) {
                e.printStackTrace();
            }
        }

    }
}
```

#### 1.2.3 新建USERJDBCService.java

```java
package com.xiaopizhu.mybatis.service;

import com.xiaopizhu.mybatis.dao.UserJDBCDao;
import com.xiaopizhu.mybatis.pojo.User;

import java.util.List;

public class UserJDBCService {

    private UserJDBCDao userJDBCDao;

    public UserJDBCService(UserJDBCDao userJDBCDao){
        this.userJDBCDao = userJDBCDao;
    }


    public List<User> findUserList(int age){
        return userJDBCDao.findUserListGtAge(age);
    }
}
```

#### 1.2.4 测试用例测试

```java
package com.xiaopizhu.mybatis.service;


import com.xiaopizhu.mybatis.dao.UserJDBCDao;
import com.xiaopizhu.mybatis.pojo.User;
import org.junit.Before;
import org.junit.Test;

import java.util.List;

public class UserJDBCServiceTest {
    private UserJDBCService userJDBCService;

    @Before
    public void setup(){
        userJDBCService = new UserJDBCService(new UserJDBCDao());
    }

    @Test
    public void testFindUserList(){
        List<User> userList = userJDBCService.findUserList(0);
        System.out.println(userList);
    }
}
```

## 2. JDBC问题

从上面的代码可以看出，JDBC的代码过于臃肿以及一些代码需要优化，需要进一步封装，减少开发人员的代码书写量，那么哪些代码是可以进行封装的？

#### 2.1 连接获取和释放

1. 问题：数据库连接频繁的开启和关闭本身就造成了**资源的浪费，影响系统的性能**
2. 解决：**数据库连接池**

#### 2.2 SQL统一存取

1. 问题：sql散落在不同的java文件中，可读性很差，不利于维护（修改sql语句，sql调优等非常麻烦）
2. 解决：不把sql写到java代码中，抽取出来，放入统一的一个地方，比如配置文件，使用id进行匹配

#### 2.3 传入的参数映射和动态sql

1. 问题：设置占位符来达到使用传入参数的目的，按照一定顺序传入参数的，要与占位符一一匹配。但是，如果我们**传入的参数是不确定的**（比如列表查询，根据用户填写的查询条件不同，传入查询的参数也是不同的，有时是一个参数、有时可能是三个参数），那么我们就得**在后台代码中自己根据请求的传入参数去拼凑相应的SQL语句**，这样的话还是**避免不了在Java代码里面写SQL语句的命运**。
2. 动态sql问题：怎么做到能够根据前台传入参数的不同，动态生成对应的SQL语句呢？
3. 解决：第一个问题，可以用**一种有别于SQL的语法来嵌入变量（比如使用＃变量名＃）**，这样，SQL语句经过解析后就可以动态的生成符合上下文的SQL语句。第二个问题，可以在sql语句中，使用 这样的标签，通过特定的sql解析器来去解析，设定类似 在test中写对应的判断语句。

#### 2.4 结果映射和结果缓存

```
1. 问题：每次执行完sql语句，要从结果集获取数据，以及要对结果进行封装，要写大量的代码
2. 解决：期望有一个sql处理器，只需要告诉它，1.需要返回什么类型的对象，2.需要返回的对象的数据结构和结果的映射，同时还可以对结果数据做缓存，提升性能
```

#### 2.5 重复sql

```
1. 问题：sql语句很多都是where语句后面的部分不一样，重复语句，无意义，查询的字段有很多也是重复的(select aaa,bbb from)
2. 解决：提取重复的sql片段
```

## 3. JDBC演变为Mybatis

mybatis是一个持久层框架，用java编写的。

它封装了jdbc操作的很多细节，使开发者只需要关注sql语句本身，而无需关注注册驱动，创建连接等繁杂过程

它使用了ORM思想实现了结果集的封装。

ORM：

Object Relational Mappging 对象关系映射

简单的说：

就是把数据库表和实体类及实体类的属性对应起来

让我们可以操作实体类就实现操作数据库表。

![](img/image-20210418130511022.png#alt=image-20210418130511022)

## 4. Mybatis的使用

以下的方式不用特别记忆，了解即可，学习完Spring之后，spring+mybatis的方式才是主流

#### 4.1 定义DAO接口

```java
package com.xiaopizhu.mybatis.dao;

import com.xiaopizhu.mybatis.pojo.User;

public interface UserDao {

    User findUser(Long id);
}
```

#### 4.2 建立Mybatis配置文件

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE configuration PUBLIC "-//mybatis.org//DTD Config 3.0//EN" "http://mybatis.org/dtd/mybatis-3-config.dtd">
<configuration>
    <!--数据源配置 -->
    <environments default="development">
        <environment id="development">
            <transactionManager type="jdbc" />
            <!--数据源 POOLED 利用“池”的概念将 JDBC 的 Connection 对象组织起来，它开始会有一些空置，并且已经连接好的数据库连接，所以请求时，无须再建立和验证，省去了创建新的连接实例时所必需的初始化和认证时间。它还控制最大连接数，避免过多的连接导致系统瓶颈。-->
            <dataSource type="POOLED">
                <property name="driver" value="com.mysql.cj.jdbc.Driver" />
                <property name="url" value="jdbc:mysql://localhost:3306/god?serverTimezone=UTC&amp;characterEncoding=utf-8" />
                <property name="username" value="root" />
                <property name="password" value="root" />
            </dataSource>
        </environment>
    </environments>
    <mappers>
        <!--userMapper.xml装载进来 -->
        <mapper resource="com/xiaopizhu/mybatis/dao/UserDao.xml" />
    </mappers>
</configuration>
```

#### 4.3 创建xml和DAO接口对应，在其中统一写sql

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!--MyBatis配置文件-->
<!DOCTYPE mapper PUBLIC "-//mybatis.org//DTD Config 3.0//EN" "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="com.xiaopizhu.mybatis.dao.UserDao">
	<!--findUser和UserDao接口中的findUser方法对应-->
    <select id="findUser" parameterType="long" resultType="com.xiaopizhu.mybatis.pojo.User">
        select * from g_user where id = #{id}
    </select>
</mapper>
```

#### 4.4 测试 并添加日志

```java
package com.xiaopizhu.mybatis.service;

import com.xiaopizhu.mybatis.dao.UserDao;
import com.xiaopizhu.mybatis.pojo.User;
import org.apache.ibatis.io.Resources;
import org.apache.ibatis.session.SqlSession;
import org.apache.ibatis.session.SqlSessionFactoryBuilder;


public class UserService {


    public User findUser(Long id){
        SqlSession sqlSession = null;
        try {
            sqlSession = new SqlSessionFactoryBuilder().build(Resources.getResourceAsReader("Mybatis-Config.xml")).openSession();
            UserDao mapper = sqlSession.getMapper(UserDao.class);
            User user = mapper.findUser(id);
            return user;
        } catch (Exception e) {
            e.printStackTrace();
        }finally {
            if (sqlSession != null){
                sqlSession.close();
            }
        }
        return null;

    }
}
```

```java
package com.xiaopizhu.mybatis.service;


import com.xiaopizhu.mybatis.dao.UserDao;
import com.xiaopizhu.mybatis.dao.UserJDBCDao;
import com.xiaopizhu.mybatis.pojo.User;
import org.junit.Before;
import org.junit.Test;

import java.util.List;

public class UserServiceTest {

    private UserService userService;

    @Before
    public void setup(){
        userService = new UserService();
    }

    @Test
    public void testFindUser(){
        User user = userService.findUser(1L);
        System.out.println(user);
    }
}
```

添加日志:

log4j.properties

```properties
## debug 级别

log4j.rootLogger=DEBUG,Console


log4j.appender.Console=org.apache.log4j.ConsoleAppender  
log4j.appender.Console.Target=System.out  
log4j.appender.Console.layout = org.apache.log4j.PatternLayout  
log4j.appender.Console.layout.ConversionPattern=%d{yyyy-MM-dd-HH\:mm\:ss,SSS} [%t]  [%c] [%p] - %m%n  

log4j.logger.com.mybatis=DEBUG  /
```

依赖:

```xml
<dependency>
            <groupId>log4j</groupId>
            <artifactId>log4j</artifactId>
            <version>1.2.12</version>
        </dependency>
```

mybatis配置文件 添加:

```xml
  <settings>
        <setting name="logImpl" value="log4j"/>
    </settings>
```

#### 4.5 insert 添加实现

```java
void save(User user);
```

```xml
 <insert id="save" parameterType="com.xiaopizhu.mybatis.pojo.User" keyProperty="id" useGeneratedKeys="true">
        insert into g_user (username,age) values(#{username},#{age})
    </insert>
```

```java
  @Test
    public void testSaveUser(){
        User user = new User();
        user.setUsername("wangwu");
        user.setAge(25);
        userService.save(user);
        System.out.println(user);
    }
```

```java
public void save(User user) {
        SqlSession sqlSession = null;
        try {
            sqlSession = new SqlSessionFactoryBuilder().build(Resources.getResourceAsReader("Mybatis-Config.xml")).openSession();
            UserDao mapper = sqlSession.getMapper(UserDao.class);
            mapper.save(user);
            sqlSession.commit();
        } catch (Exception e) {
            e.printStackTrace();
        }finally {
            if (sqlSession != null){
                sqlSession.close();
            }
        }
    }
```

keyProperty="id" useGeneratedKeys="true" 代表将自增的id 辅助给User对象的id属性

#### 4.6 update 更新实现

```java
int update(User user);
```

```xml
 <update id="update" parameterType="com.xiaopizhu.mybatis.pojo.User">
        update g_user
        set username=#{username},
            age=#{age}
        where id=#{id}
    </update>
```

```java
 public void updateUser(User user) {
        SqlSession sqlSession = null;
        try {
            sqlSession = new SqlSessionFactoryBuilder().build(Resources.getResourceAsReader("Mybatis-Config.xml")).openSession();
            UserDao mapper = sqlSession.getMapper(UserDao.class);
            int update = mapper.update(user);
            sqlSession.commit();
            System.out.println("更新行数,"+update);
        } catch (Exception e) {
            e.printStackTrace();
        }finally {
            if (sqlSession != null){
                sqlSession.close();
            }
        }
    }
```

```java
   @Test
    public void testUpdateUser(){
        User user = new User();
        user.setUsername("wangwu333");
        user.setAge(25);
        user.setId(5L);
        userService.updateUser(user);
        System.out.println(user);
    }
```

#### 4.7 DELETE 删除实现

```java
    int delete(Long id);
```

```xml
 <delete id="delete" parameterType="long">
        delete from g_user where id=#{id}
    </delete>
```

```java
public void delete(Long id) {
        SqlSession sqlSession = null;
        try {
            sqlSession = new SqlSessionFactoryBuilder().build(Resources.getResourceAsReader("Mybatis-Config.xml")).openSession();
            UserDao mapper = sqlSession.getMapper(UserDao.class);
            int delete = mapper.delete(id);
            sqlSession.commit();
            System.out.println("删除行数,"+delete);
        } catch (Exception e) {
            e.printStackTrace();
        }finally {
            if (sqlSession != null){
                sqlSession.close();
            }
        }
    }
```

```java
@Test
    public void testDelete(){
        userService.delete(5L);
    }
```

#### 4.8 列表查询实现

```java
List<User> findUserListGtAge(int age);
```

```xml
<select id="findUserListGtAge" parameterType="int" resultType="com.xiaopizhu.mybatis.pojo.User">
        select * from g_user where age > #{age}
    </select>
```

```java
public List<User> findUserList(int age) {
        SqlSession sqlSession = null;
        try {
            sqlSession = new SqlSessionFactoryBuilder().build(Resources.getResourceAsReader("Mybatis-Config.xml")).openSession();
            UserDao mapper = sqlSession.getMapper(UserDao.class);
            List<User> userListGtAge = mapper.findUserListGtAge(age);
            return userListGtAge;
        } catch (Exception e) {
            e.printStackTrace();
        }finally {
            if (sqlSession != null){
                sqlSession.close();
            }
        }
        return new ArrayList<>();
    }
```

```java
  @Test
    public void testFindUserList(){
        List<User> userList = userService.findUserList(0);
        System.out.println(userList);
    }
```

#### 4.9 分页实现

##### 4.9.1 普通实现

```java
  long countUserListPage();

    List<User> findUserListPage(@Param("index") int index,
                                @Param("size")int size);
```

```xml
<select id="findUserListPage" parameterType="map" resultType="com.xiaopizhu.mybatis.pojo.User">
        select *from g_user limit #{index},#{size}
    </select>
    <select id="countUserListPage" resultType="java.lang.Long">
        select count(1) from g_user
    </select>
```

```java
 public List<User> findUserListPage(int page,int size) {
        SqlSession sqlSession = null;
        try {
            sqlSession = new SqlSessionFactoryBuilder().build(Resources.getResourceAsReader("Mybatis-Config.xml")).openSession();
            UserDao mapper = sqlSession.getMapper(UserDao.class);
            int index = (page-1)*size;
            List<User> userList = mapper.findUserListPage(index,size);
            long total = mapper.countUserListPage();
            System.out.println("总数为:"+total);
            return userList;
        } catch (Exception e) {
            e.printStackTrace();
        }finally {
            if (sqlSession != null){
                sqlSession.close();
            }
        }
        return new ArrayList<>();
    }
```

```java
 @Test
    public void testFindUserListPage(){
        List<User> userList = userService.findUserListPage(1,1);
        System.out.println(userList);
    }
```

##### 4.9.2 分页插件实现

```xml
<dependency>
            <groupId>com.github.pagehelper</groupId>
            <artifactId>pagehelper</artifactId>
            <version>5.2.0</version>
        </dependency>
```

```xml
 <plugins>
        <plugin interceptor="com.github.pagehelper.PageInterceptor">
        </plugin>
    </plugins>
```

```java
    Page<User> findUserList();
```

```xml
  <select id="findUserList" resultType="com.xiaopizhu.mybatis.pojo.User">
        select *from g_user
    </select>
```

```java
 public List<User> findUserList(int page,int size) {
        SqlSession sqlSession = null;
        try {
            sqlSession = new SqlSessionFactoryBuilder().build(Resources.getResourceAsReader("Mybatis-Config.xml")).openSession();
            UserDao mapper = sqlSession.getMapper(UserDao.class);
            PageHelper.startPage(page,size);
            Page<User> userListPage = mapper.findUserList();
            System.out.println("总数为:"+userListPage.getTotal());
            List<User> result = userListPage.getResult();
            return result;
        } catch (Exception e) {
            e.printStackTrace();
        }finally {
            if (sqlSession != null){
                sqlSession.close();
            }
        }
        return new ArrayList<>();
    }
```

```java
 @Test
    public void testFindUserListByPage(){
        List<User> userList = userService.findUserList(1,1);
        System.out.println(userList);
    }
```

#### 4.10 各种标签的使用

##### 4.10.1 where和if标签

```java
List<User> findUserListByCondition(int age);
```

```xml
<!-- 注意看 where 后面的第一个and 通过日志打印的sql会发现and会消失，这就是where标签的作用-->
<select id="findUserListByCondition" parameterType="int" resultType="com.xiaopizhu.mybatis.pojo.User">
        select *from g_user
        <where>
            <if test="age > 0">
              and  age > 0
            </if>
            <if test="age &lt; 15">
               and age &lt; 22
            </if>
        </where>
    </select>
```

```java
 public List<User> findUserListCondition(int age) {
        SqlSession sqlSession = null;
        try {
            sqlSession = new SqlSessionFactoryBuilder().build(Resources.getResourceAsReader("Mybatis-Config.xml")).openSession();
            UserDao mapper = sqlSession.getMapper(UserDao.class);
            List<User> userList = mapper.findUserListByCondition(age);
            return userList;
        } catch (Exception e) {
            e.printStackTrace();
        }finally {
            if (sqlSession != null){
                sqlSession.close();
            }
        }
        return new ArrayList<>();
    }
```

```java
@Test
    public void testFindUserListCondition(){
        List<User> userList = userService.findUserListCondition(5);
        System.out.println(userList);
    }
```

##### 4.10.2  foreach标签

```java
List<User> findUserListByIds(@Param("userIdList") List<Long> userIdList);
```

```xml
<select id="findUserListByIds" parameterType="arraylist" resultType="com.xiaopizhu.mybatis.pojo.User">
        select *from g_user
        <where>
            <if test="userIdList != null and userIdList.size > 0">
                id in
                <foreach collection="userIdList" item="userId" separator="," open="(" close=")">
                    #{userId}
                </foreach>
            </if>
        </where>
    </select>
```

```java
 public List<User> findUserListByIds(List<Long> userIdList) {
        SqlSession sqlSession = null;
        try {
            sqlSession = new SqlSessionFactoryBuilder().build(Resources.getResourceAsReader("Mybatis-Config.xml")).openSession();
            UserDao mapper = sqlSession.getMapper(UserDao.class);
            List<User> userList = mapper.findUserListByIds(userIdList);
            return userList;
        } catch (Exception e) {
            e.printStackTrace();
        }finally {
            if (sqlSession != null){
                sqlSession.close();
            }
        }
        return new ArrayList<>();
    }
```

```java
  @Test
    public void testFindUserListByIds(){
        List<Long> userIdList = new ArrayList<>();
        userIdList.add(1L);
        userIdList.add(2L);
        List<User> userList = userService.findUserListByIds(userIdList);
        System.out.println(userList);
    }
```

##### 4.10.3 set标签

```xml
<update id="update" parameterType="com.xiaopizhu.mybatis.pojo.User">
        update g_user
        <set>
            username=#{username},
            age=#{age},
        </set>
        where id=#{id}
    </update>
```

使用set标签，后面多一个逗号 也无所谓

#### 4.11 ResultMap的使用

比如 g_user表中 多了一个字段 为id_card 身份证，这时候使用resultType="com.xiaopizhu.mybatis.pojo.User" 是无法进行映射到User对象的idCard字段的。

这种场景下，就需要定义ResultMap 做对象和表字段的对应关系

```xml
 <resultMap id="user" type="com.xiaopizhu.mybatis.pojo.User">
        <id column="id" property="id"/>
        <result column="username" property="username"/>
        <result column="age" property="age"/>
        <result column="id_card" property="idCard"/>
    </resultMap>
```

```xml
 <select id="findUserListByIds" parameterType="arraylist" resultMap="user">
        select * from g_user
        <where>
            <if test="userIdList != null and userIdList.size > 0">
                id in
                <foreach collection="userIdList" item="userId" separator="," open="(" close=")">
                    #{userId}
                </foreach>
            </if>
        </where>
    </select>
```

#### 4.12 事务

需求：之前实现的service方法 updateUser(User user)，在这个方法运行的过程当中 出现异常了，异常出现的位置在userDao.update(user) 之后，这时候，我们希望 对此数据进行回滚，已经更新的数据，回退回原来的数据

```java
public int updateUser(User user){
        SqlSession sqlSession = null;
        try {
            //1. 获取mybatis的配置文件 并加载
            Reader resourceAsReader = Resources.getResourceAsReader("MybatisConfig.xml");
            //2. 构建sqlSession工厂
            SqlSessionFactory build = new SqlSessionFactoryBuilder().build(resourceAsReader);
            //3. 生产sqlSession true代表自动提交
            sqlSession = build.openSession(true);
            //4. 获取UserDao
            UserDao userDao = sqlSession.getMapper(UserDao.class);
            int update = userDao.update(user);
            //事务处理 抛异常了
            int i = 10/0;
            return update;
        } catch (IOException e) {
            e.printStackTrace();
        }finally {
            if (sqlSession != null){
                sqlSession.close();
            }
        }
        return -1;
    }
```

如果运行以上的代码，发现 虽然报了异常，但是数据还是进行了更新，那么这时候我们该怎么办呢？

首先：

1. 自动提交不能使用回滚
2. 已经提交的数据不能回滚

```java
public int updateUser(User user){
        SqlSession sqlSession = null;
        try {
            //1. 获取mybatis的配置文件 并加载
            Reader resourceAsReader = Resources.getResourceAsReader("MybatisConfig.xml");
            //2. 构建sqlSession工厂
            SqlSessionFactory build = new SqlSessionFactoryBuilder().build(resourceAsReader);
            //3. 生产sqlSession true代表自动提交
            sqlSession = build.openSession();
            //4. 获取UserDao
            UserDao userDao = sqlSession.getMapper(UserDao.class);
            int update = userDao.update(user);
            //事务 抛异常了
            int i = 10/0;
            sqlSession.commit();
            return update;
        } catch (Exception e) {
            e.printStackTrace();
            //回滚
            sqlSession.rollback();
        }finally {
            if (sqlSession != null){
                sqlSession.close();
            }
        }
        return -1;
    }
```

一旦抛了异常，可以进行回滚使用sqlSession.rollback(); 数据就进行了回滚

#### 4.13 sql注入问题

在UserDao中写一个方法

```java
    User findUserByName(String username);
```

```java
public User findUserByName(String userName){
        SqlSession sqlSession = null;
        try {
            //1. 获取mybatis的配置文件 并加载
            Reader resourceAsReader = Resources.getResourceAsReader("MybatisConfig.xml");
            //2. 构建sqlSession工厂
            SqlSessionFactory build = new SqlSessionFactoryBuilder().build(resourceAsReader);
            //3. 生产sqlSession
            sqlSession = build.openSession();
            //4. 获取UserDao
            UserDao userDao = sqlSession.getMapper(UserDao.class);
            User user = userDao.findUserByName(userName);
            return user;
        } catch (IOException e) {
            e.printStackTrace();
        }finally {
            if (sqlSession != null){
                sqlSession.close();
            }
        }
        return null;
    }
```

```java
  @Test
    public void testFindUserName(){
        User user = userService.findUserByName("'zhangsan' or username='lisi'");
        System.out.println(user);
    }
```

```xml
<select id="findUserByName" parameterType="string" resultType="com.xiaopizhu.mybatis.pojo.User">
        select * from g_user where username = ${username}
    </select>
```

## 5. Mybatis的原理

![](img/image-20210418130511022.png#alt=image-20210418130511022)

Mybatis的功能架构分为三层：

- 接口层：提供给外部使用的接口API，开发人员通过这些本地API来操作数据库。接口层接收到调用请求就会调用数据处理层来完成具体的数据处理。
- 数据处理层：负责具体的SQL查找、SQL解析、SQL执行和执行结果映射处理等。它主要的目的是根据调用的请求完成一次数据库操作。
- 框架支撑层：负责最基础的功能支撑，包括连接管理、事务管理、配置加载和缓存处理，这些都是共用的东西，将他们抽取出来作为最基础的组件。为上层的数据处理层提供最基础的支撑。

![](img/mybatis.png#alt=%E5%9B%BE%E7%89%87)

- SqlSession。作为MyBatis工作的主要顶层API，表示和数据库交互的会话，完成必要数据库增删改查功能。
- Executor。MyBatis执行器，是MyBatis调度的核心，负责SQL语句的生成和查询缓存的维护。
- StatementHandler。封装了JDBC Statement操作，负责对JDBCstatement的操作，如设置参数、将Statement结果集转换成List集合。
- ParameterHandler。负责对用户传递的参数转换成JDBC Statement 所需要的参数。
- ResultSetHandler。负责将JDBC返回的ResultSet结果集对象转换成List类型的集合。
- TypeHandler 负责java数据类型和jdbc数据类型之间的映射和转换。
- MappedStatement。MappedStatement维护了一条<select|update|delete|insert>节点的封装。
- SqlSource。负责根据用户传递的parameterObject，动态地生成SQL语句，将信息封装到BoundSql对象中，并返回。
- BoundSql。表示动态生成的SQL语句以及相应的参数信息。
- Configuration。MyBatis所有的配置信息都维持在Configuration对象之中。

##### 5.1 TypeHandler

	无论是 MyBatis 在预处理语句（PreparedStatement）中设置一个参数时，还是从结果集中取出一个值时， 都会用类型处理器将获取的值以合适的方式转换成 Java 类型。

![](img/640.webp#alt=%E5%9B%BE%E7%89%87)

以BigDecimalTypeHandler为例子:

```java
/**
 *    Copyright 2009-2015 the original author or authors.
 *
 *    Licensed under the Apache License, Version 2.0 (the "License");
 *    you may not use this file except in compliance with the License.
 *    You may obtain a copy of the License at
 *
 *       http://www.apache.org/licenses/LICENSE-2.0
 *
 *    Unless required by applicable law or agreed to in writing, software
 *    distributed under the License is distributed on an "AS IS" BASIS,
 *    WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
 *    See the License for the specific language governing permissions and
 *    limitations under the License.
 */
package org.apache.ibatis.type;

import java.math.BigDecimal;
import java.sql.CallableStatement;
import java.sql.PreparedStatement;
import java.sql.ResultSet;
import java.sql.SQLException;

/**
 * @author Clinton Begin
 */
public class BigDecimalTypeHandler extends BaseTypeHandler<BigDecimal> {

  @Override
  public void setNonNullParameter(PreparedStatement ps, int i, BigDecimal parameter, JdbcType jdbcType)
      throws SQLException {
    ps.setBigDecimal(i, parameter);
  }

  @Override
  public BigDecimal getNullableResult(ResultSet rs, String columnName)
      throws SQLException {
    return rs.getBigDecimal(columnName);
  }

  @Override
  public BigDecimal getNullableResult(ResultSet rs, int columnIndex)
      throws SQLException {
    return rs.getBigDecimal(columnIndex);
  }

  @Override
  public BigDecimal getNullableResult(CallableStatement cs, int columnIndex)
      throws SQLException {
    return cs.getBigDecimal(columnIndex);
  }
}
```

这个类的第一个方法是对预处理语句（PreparedStatement)设置参数，之后的三个函数都是从ResultSet或者用于执行存储过程的CallableStatement语句中获取BigDecimal类型的数值，用于向BigDecimal类型的Java字段赋值。

BigDecimalTypeHandler继承的BaseTypeHandler是个泛型类，其他的TypeHandler也是通过继承这个抽象类，实现其中的抽象方法，实现类型转换的工作。

```java
/**
 *    Copyright 2009-2020 the original author or authors.
 *
 *    Licensed under the Apache License, Version 2.0 (the "License");
 *    you may not use this file except in compliance with the License.
 *    You may obtain a copy of the License at
 *
 *       http://www.apache.org/licenses/LICENSE-2.0
 *
 *    Unless required by applicable law or agreed to in writing, software
 *    distributed under the License is distributed on an "AS IS" BASIS,
 *    WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
 *    See the License for the specific language governing permissions and
 *    limitations under the License.
 */
package org.apache.ibatis.type;

import java.sql.CallableStatement;
import java.sql.PreparedStatement;
import java.sql.ResultSet;
import java.sql.SQLException;

import org.apache.ibatis.executor.result.ResultMapException;
import org.apache.ibatis.session.Configuration;

/**
 * The base {@link TypeHandler} for references a generic type.
 * <p>
 * Important: Since 3.5.0, This class never call the {@link ResultSet#wasNull()} and
 * {@link CallableStatement#wasNull()} method for handling the SQL {@code NULL} value.
 * In other words, {@code null} value handling should be performed on subclass.
 * </p>
 *
 * @author Clinton Begin
 * @author Simone Tripodi
 * @author Kzuki Shimizu
 */
public abstract class BaseTypeHandler<T> extends TypeReference<T> implements TypeHandler<T> {

  /**
   * @deprecated Since 3.5.0 - See https://github.com/mybatis/mybatis-3/issues/1203. This field will remove future.
   */
  @Deprecated
  protected Configuration configuration;

  /**
   * Sets the configuration.
   *
   * @param c
   *          the new configuration
   * @deprecated Since 3.5.0 - See https://github.com/mybatis/mybatis-3/issues/1203. This property will remove future.
   */
  @Deprecated
  public void setConfiguration(Configuration c) {
    this.configuration = c;
  }

  @Override
  public void setParameter(PreparedStatement ps, int i, T parameter, JdbcType jdbcType) throws SQLException {
    if (parameter == null) {
      if (jdbcType == null) {
        throw new TypeException("JDBC requires that the JdbcType must be specified for all nullable parameters.");
      }
      try {
        ps.setNull(i, jdbcType.TYPE_CODE);
      } catch (SQLException e) {
        throw new TypeException("Error setting null for parameter #" + i + " with JdbcType " + jdbcType + " . "
              + "Try setting a different JdbcType for this parameter or a different jdbcTypeForNull configuration property. "
              + "Cause: " + e, e);
      }
    } else {
      try {
        setNonNullParameter(ps, i, parameter, jdbcType);
      } catch (Exception e) {
        throw new TypeException("Error setting non null for parameter #" + i + " with JdbcType " + jdbcType + " . "
              + "Try setting a different JdbcType for this parameter or a different configuration property. "
              + "Cause: " + e, e);
      }
    }
  }

  @Override
  public T getResult(ResultSet rs, String columnName) throws SQLException {
    try {
      return getNullableResult(rs, columnName);
    } catch (Exception e) {
      throw new ResultMapException("Error attempting to get column '" + columnName + "' from result set.  Cause: " + e, e);
    }
  }

  @Override
  public T getResult(ResultSet rs, int columnIndex) throws SQLException {
    try {
      return getNullableResult(rs, columnIndex);
    } catch (Exception e) {
      throw new ResultMapException("Error attempting to get column #" + columnIndex + " from result set.  Cause: " + e, e);
    }
  }

  @Override
  public T getResult(CallableStatement cs, int columnIndex) throws SQLException {
    try {
      return getNullableResult(cs, columnIndex);
    } catch (Exception e) {
      throw new ResultMapException("Error attempting to get column #" + columnIndex + " from callable statement.  Cause: " + e, e);
    }
  }

  public abstract void setNonNullParameter(PreparedStatement ps, int i, T parameter, JdbcType jdbcType) throws SQLException;

  /**
   * Gets the nullable result.
   *
   * @param rs
   *          the rs
   * @param columnName
   *          Colunm name, when configuration <code>useColumnLabel</code> is <code>false</code>
   * @return the nullable result
   * @throws SQLException
   *           the SQL exception
   */
  public abstract T getNullableResult(ResultSet rs, String columnName) throws SQLException;

  public abstract T getNullableResult(ResultSet rs, int columnIndex) throws SQLException;

  public abstract T getNullableResult(CallableStatement cs, int columnIndex) throws SQLException;

}
```

这个抽象类实现了TypeHandler接口，这个接口主要定义了类型转换的几种操作。

```java
/**
 *    Copyright 2009-2020 the original author or authors.
 *
 *    Licensed under the Apache License, Version 2.0 (the "License");
 *    you may not use this file except in compliance with the License.
 *    You may obtain a copy of the License at
 *
 *       http://www.apache.org/licenses/LICENSE-2.0
 *
 *    Unless required by applicable law or agreed to in writing, software
 *    distributed under the License is distributed on an "AS IS" BASIS,
 *    WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
 *    See the License for the specific language governing permissions and
 *    limitations under the License.
 */
package org.apache.ibatis.type;

import java.sql.CallableStatement;
import java.sql.PreparedStatement;
import java.sql.ResultSet;
import java.sql.SQLException;

/**
 * @author Clinton Begin
 */
public interface TypeHandler<T> {

  void setParameter(PreparedStatement ps, int i, T parameter, JdbcType jdbcType) throws SQLException;

  /**
   * Gets the result.
   *
   * @param rs
   *          the rs
   * @param columnName
   *          Colunm name, when configuration <code>useColumnLabel</code> is <code>false</code>
   * @return the result
   * @throws SQLException
   *           the SQL exception
   */
  T getResult(ResultSet rs, String columnName) throws SQLException;

  T getResult(ResultSet rs, int columnIndex) throws SQLException;

  T getResult(CallableStatement cs, int columnIndex) throws SQLException;

}
```

至于这个抽象类继承的TypeReference，主要是提供了获取这个T具体是哪个类型。在判断使用使用哪个TypeHandler时有用。

```java
/**
 *    Copyright 2009-2016 the original author or authors.
 *
 *    Licensed under the Apache License, Version 2.0 (the "License");
 *    you may not use this file except in compliance with the License.
 *    You may obtain a copy of the License at
 *
 *       http://www.apache.org/licenses/LICENSE-2.0
 *
 *    Unless required by applicable law or agreed to in writing, software
 *    distributed under the License is distributed on an "AS IS" BASIS,
 *    WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
 *    See the License for the specific language governing permissions and
 *    limitations under the License.
 */
package org.apache.ibatis.type;

import java.lang.reflect.ParameterizedType;
import java.lang.reflect.Type;

/**
 * References a generic type.
 *
 * @param <T> the referenced type
 * @since 3.1.0
 * @author Simone Tripodi
 */
public abstract class TypeReference<T> {

  private final Type rawType;

  protected TypeReference() {
    rawType = getSuperclassTypeParameter(getClass());
  }

  Type getSuperclassTypeParameter(Class<?> clazz) {
    Type genericSuperclass = clazz.getGenericSuperclass();
    if (genericSuperclass instanceof Class) {
      // try to climb up the hierarchy until meet something useful
      if (TypeReference.class != genericSuperclass) {
        return getSuperclassTypeParameter(clazz.getSuperclass());
      }

      throw new TypeException("'" + getClass() + "' extends TypeReference but misses the type parameter. "
        + "Remove the extension or add a type parameter to it.");
    }

    Type rawType = ((ParameterizedType) genericSuperclass).getActualTypeArguments()[0];
    // TODO remove this when Reflector is fixed to return Types
    if (rawType instanceof ParameterizedType) {
      rawType = ((ParameterizedType) rawType).getRawType();
    }

    return rawType;
  }

  public final Type getRawType() {
    return rawType;
  }

  @Override
  public String toString() {
    return rawType.toString();
  }

}
```

首先Mybatis有一个默认的TypeHandler实现，这些TypeHandler是如何被Mybatis识别的呢？

答案是TypeHandlerRegistry。在Mybatis初始化配置的时候，TypeHandlerRegistry会把JdbcType和Java类型对应的映射关系注册进该类内部的Map中。

JDBC_TYPE_HANDLER_MAP中记录的是JdbcType和TypeHandler对应的关系。

TYPE_HANDLER_MAP中记录的是Java类型和对应的所有JdbcType以及其对应TypeHandler的映射关系关系。

UNKNOWN_TYPE_HANDLER是在执行BaseTypeHandler的抽象方法时，去先解析出来该用什么TypeHandler。

ALL_TYPE_HANDLERS_MAP中记录的是所有TypeHandler的Class和其实例之间的映射关系。

```java
  private final Map<JdbcType, TypeHandler<?>>  jdbcTypeHandlerMap = new EnumMap<>(JdbcType.class);
  private final Map<Type, Map<JdbcType, TypeHandler<?>>> typeHandlerMap = new ConcurrentHashMap<>();
  private final TypeHandler<Object> unknownTypeHandler;
  private final Map<Class<?>, TypeHandler<?>> allTypeHandlersMap = new HashMap<>();

public TypeHandlerRegistry(Configuration configuration) {
    this.unknownTypeHandler = new UnknownTypeHandler(configuration);

    register(Boolean.class, new BooleanTypeHandler());
    register(boolean.class, new BooleanTypeHandler());
    register(JdbcType.BOOLEAN, new BooleanTypeHandler());
    register(JdbcType.BIT, new BooleanTypeHandler());

    register(Byte.class, new ByteTypeHandler());
    register(byte.class, new ByteTypeHandler());
    register(JdbcType.TINYINT, new ByteTypeHandler());

    register(Short.class, new ShortTypeHandler());
    register(short.class, new ShortTypeHandler());
    register(JdbcType.SMALLINT, new ShortTypeHandler());

    register(Integer.class, new IntegerTypeHandler());
    register(int.class, new IntegerTypeHandler());
    register(JdbcType.INTEGER, new IntegerTypeHandler());

    register(Long.class, new LongTypeHandler());
    register(long.class, new LongTypeHandler());

    register(Float.class, new FloatTypeHandler());
    register(float.class, new FloatTypeHandler());
    register(JdbcType.FLOAT, new FloatTypeHandler());

    register(Double.class, new DoubleTypeHandler());
    register(double.class, new DoubleTypeHandler());
    register(JdbcType.DOUBLE, new DoubleTypeHandler());

    register(Reader.class, new ClobReaderTypeHandler());
    register(String.class, new StringTypeHandler());
    register(String.class, JdbcType.CHAR, new StringTypeHandler());
    register(String.class, JdbcType.CLOB, new ClobTypeHandler());
    register(String.class, JdbcType.VARCHAR, new StringTypeHandler());
    register(String.class, JdbcType.LONGVARCHAR, new StringTypeHandler());
    register(String.class, JdbcType.NVARCHAR, new NStringTypeHandler());
    register(String.class, JdbcType.NCHAR, new NStringTypeHandler());
    register(String.class, JdbcType.NCLOB, new NClobTypeHandler());
    register(JdbcType.CHAR, new StringTypeHandler());
    register(JdbcType.VARCHAR, new StringTypeHandler());
    register(JdbcType.CLOB, new ClobTypeHandler());
    register(JdbcType.LONGVARCHAR, new StringTypeHandler());
    register(JdbcType.NVARCHAR, new NStringTypeHandler());
    register(JdbcType.NCHAR, new NStringTypeHandler());
    register(JdbcType.NCLOB, new NClobTypeHandler());

    register(Object.class, JdbcType.ARRAY, new ArrayTypeHandler());
    register(JdbcType.ARRAY, new ArrayTypeHandler());

    register(BigInteger.class, new BigIntegerTypeHandler());
    register(JdbcType.BIGINT, new LongTypeHandler());

    register(BigDecimal.class, new BigDecimalTypeHandler());
    register(JdbcType.REAL, new BigDecimalTypeHandler());
    register(JdbcType.DECIMAL, new BigDecimalTypeHandler());
    register(JdbcType.NUMERIC, new BigDecimalTypeHandler());

    register(InputStream.class, new BlobInputStreamTypeHandler());
    register(Byte[].class, new ByteObjectArrayTypeHandler());
    register(Byte[].class, JdbcType.BLOB, new BlobByteObjectArrayTypeHandler());
    register(Byte[].class, JdbcType.LONGVARBINARY, new BlobByteObjectArrayTypeHandler());
    register(byte[].class, new ByteArrayTypeHandler());
    register(byte[].class, JdbcType.BLOB, new BlobTypeHandler());
    register(byte[].class, JdbcType.LONGVARBINARY, new BlobTypeHandler());
    register(JdbcType.LONGVARBINARY, new BlobTypeHandler());
    register(JdbcType.BLOB, new BlobTypeHandler());

    register(Object.class, unknownTypeHandler);
    register(Object.class, JdbcType.OTHER, unknownTypeHandler);
    register(JdbcType.OTHER, unknownTypeHandler);

    register(Date.class, new DateTypeHandler());
    register(Date.class, JdbcType.DATE, new DateOnlyTypeHandler());
    register(Date.class, JdbcType.TIME, new TimeOnlyTypeHandler());
    register(JdbcType.TIMESTAMP, new DateTypeHandler());
    register(JdbcType.DATE, new DateOnlyTypeHandler());
    register(JdbcType.TIME, new TimeOnlyTypeHandler());

    register(java.sql.Date.class, new SqlDateTypeHandler());
    register(java.sql.Time.class, new SqlTimeTypeHandler());
    register(java.sql.Timestamp.class, new SqlTimestampTypeHandler());

    register(String.class, JdbcType.SQLXML, new SqlxmlTypeHandler());

    register(Instant.class, new InstantTypeHandler());
    register(LocalDateTime.class, new LocalDateTimeTypeHandler());
    register(LocalDate.class, new LocalDateTypeHandler());
    register(LocalTime.class, new LocalTimeTypeHandler());
    register(OffsetDateTime.class, new OffsetDateTimeTypeHandler());
    register(OffsetTime.class, new OffsetTimeTypeHandler());
    register(ZonedDateTime.class, new ZonedDateTimeTypeHandler());
    register(Month.class, new MonthTypeHandler());
    register(Year.class, new YearTypeHandler());
    register(YearMonth.class, new YearMonthTypeHandler());
    register(JapaneseDate.class, new JapaneseDateTypeHandler());

    // issue #273
    register(Character.class, new CharacterTypeHandler());
    register(char.class, new CharacterTypeHandler());
  }
```

如果自定义一个TypeHandler 如何被Mybatis识别?

Mybatis在应用中启动时，会根据XML文件初始化配置，负责解析XML生成配置类的就是XMLConfigBuilder，通过调用其中的parseConfiguration方法填充配置类。

在Mybatis中配置了 标签 那么就会识别到。

```java
private void parseConfiguration(XNode root) {
    try {
      // issue #117 read properties first
      propertiesElement(root.evalNode("properties"));
      Properties settings = settingsAsProperties(root.evalNode("settings"));
      loadCustomVfs(settings);
      loadCustomLogImpl(settings);
      typeAliasesElement(root.evalNode("typeAliases"));
      pluginElement(root.evalNode("plugins"));
      objectFactoryElement(root.evalNode("objectFactory"));
      objectWrapperFactoryElement(root.evalNode("objectWrapperFactory"));
      reflectorFactoryElement(root.evalNode("reflectorFactory"));
      settingsElement(settings);
      // read it after objectFactory and objectWrapperFactory issue #631
      environmentsElement(root.evalNode("environments"));
      databaseIdProviderElement(root.evalNode("databaseIdProvider"));
      typeHandlerElement(root.evalNode("typeHandlers"));
      mapperElement(root.evalNode("mappers"));
    } catch (Exception e) {
      throw new BuilderException("Error parsing SQL Mapper Configuration. Cause: " + e, e);
    }
  }
```

##### 5.2 ResultSetHandler

```java
/**
 *    Copyright 2009-2019 the original author or authors.
 *
 *    Licensed under the Apache License, Version 2.0 (the "License");
 *    you may not use this file except in compliance with the License.
 *    You may obtain a copy of the License at
 *
 *       http://www.apache.org/licenses/LICENSE-2.0
 *
 *    Unless required by applicable law or agreed to in writing, software
 *    distributed under the License is distributed on an "AS IS" BASIS,
 *    WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
 *    See the License for the specific language governing permissions and
 *    limitations under the License.
 */
package org.apache.ibatis.executor.resultset;

import java.sql.CallableStatement;
import java.sql.SQLException;
import java.sql.Statement;
import java.util.List;

import org.apache.ibatis.cursor.Cursor;

/**
 * @author Clinton Begin
 */
public interface ResultSetHandler {

  <E> List<E> handleResultSets(Statement stmt) throws SQLException;

  <E> Cursor<E> handleCursorResultSets(Statement stmt) throws SQLException;

  void handleOutputParameters(CallableStatement cs) throws SQLException;

}
```

##### 5.3 ParameterHandler

```java
/**
 *    Copyright 2009-2020 the original author or authors.
 *
 *    Licensed under the Apache License, Version 2.0 (the "License");
 *    you may not use this file except in compliance with the License.
 *    You may obtain a copy of the License at
 *
 *       http://www.apache.org/licenses/LICENSE-2.0
 *
 *    Unless required by applicable law or agreed to in writing, software
 *    distributed under the License is distributed on an "AS IS" BASIS,
 *    WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
 *    See the License for the specific language governing permissions and
 *    limitations under the License.
 */
package org.apache.ibatis.executor.parameter;

import java.sql.PreparedStatement;
import java.sql.SQLException;

/**
 * A parameter handler sets the parameters of the {@code PreparedStatement}.
 *
 * @author Clinton Begin
 */
public interface ParameterHandler {

  Object getParameterObject();

  void setParameters(PreparedStatement ps) throws SQLException;

}
```

```java
/**
 *    Copyright 2009-2019 the original author or authors.
 *
 *    Licensed under the Apache License, Version 2.0 (the "License");
 *    you may not use this file except in compliance with the License.
 *    You may obtain a copy of the License at
 *
 *       http://www.apache.org/licenses/LICENSE-2.0
 *
 *    Unless required by applicable law or agreed to in writing, software
 *    distributed under the License is distributed on an "AS IS" BASIS,
 *    WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
 *    See the License for the specific language governing permissions and
 *    limitations under the License.
 */
package org.apache.ibatis.scripting.defaults;

import java.sql.PreparedStatement;
import java.sql.SQLException;
import java.util.List;

import org.apache.ibatis.executor.ErrorContext;
import org.apache.ibatis.executor.parameter.ParameterHandler;
import org.apache.ibatis.mapping.BoundSql;
import org.apache.ibatis.mapping.MappedStatement;
import org.apache.ibatis.mapping.ParameterMapping;
import org.apache.ibatis.mapping.ParameterMode;
import org.apache.ibatis.reflection.MetaObject;
import org.apache.ibatis.session.Configuration;
import org.apache.ibatis.type.JdbcType;
import org.apache.ibatis.type.TypeException;
import org.apache.ibatis.type.TypeHandler;
import org.apache.ibatis.type.TypeHandlerRegistry;

/**
 * @author Clinton Begin
 * @author Eduardo Macarron
 */
public class DefaultParameterHandler implements ParameterHandler {

  private final TypeHandlerRegistry typeHandlerRegistry;

  private final MappedStatement mappedStatement;
  private final Object parameterObject;
  private final BoundSql boundSql;
  private final Configuration configuration;

  public DefaultParameterHandler(MappedStatement mappedStatement, Object parameterObject, BoundSql boundSql) {
    this.mappedStatement = mappedStatement;
    this.configuration = mappedStatement.getConfiguration();
    this.typeHandlerRegistry = mappedStatement.getConfiguration().getTypeHandlerRegistry();
    this.parameterObject = parameterObject;
    this.boundSql = boundSql;
  }

  @Override
  public Object getParameterObject() {
    return parameterObject;
  }

  @Override
  public void setParameters(PreparedStatement ps) {
    ErrorContext.instance().activity("setting parameters").object(mappedStatement.getParameterMap().getId());
    List<ParameterMapping> parameterMappings = boundSql.getParameterMappings();
    if (parameterMappings != null) {
      for (int i = 0; i < parameterMappings.size(); i++) {
        ParameterMapping parameterMapping = parameterMappings.get(i);
        if (parameterMapping.getMode() != ParameterMode.OUT) {
          Object value;
          String propertyName = parameterMapping.getProperty();
          if (boundSql.hasAdditionalParameter(propertyName)) { // issue #448 ask first for additional params
            value = boundSql.getAdditionalParameter(propertyName);
          } else if (parameterObject == null) {
            value = null;
          } else if (typeHandlerRegistry.hasTypeHandler(parameterObject.getClass())) {
            value = parameterObject;
          } else {
            MetaObject metaObject = configuration.newMetaObject(parameterObject);
            value = metaObject.getValue(propertyName);
          }
          TypeHandler typeHandler = parameterMapping.getTypeHandler();
          JdbcType jdbcType = parameterMapping.getJdbcType();
          if (value == null && jdbcType == null) {
            jdbcType = configuration.getJdbcTypeForNull();
          }
          try {
            typeHandler.setParameter(ps, i + 1, value, jdbcType);
          } catch (TypeException | SQLException e) {
            throw new TypeException("Could not set parameters for mapping: " + parameterMapping + ". Cause: " + e, e);
          }
        }
      }
    }
  }

}
```

##### 5.4 BoundSql

```java
/**
 *    Copyright 2009-2019 the original author or authors.
 *
 *    Licensed under the Apache License, Version 2.0 (the "License");
 *    you may not use this file except in compliance with the License.
 *    You may obtain a copy of the License at
 *
 *       http://www.apache.org/licenses/LICENSE-2.0
 *
 *    Unless required by applicable law or agreed to in writing, software
 *    distributed under the License is distributed on an "AS IS" BASIS,
 *    WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
 *    See the License for the specific language governing permissions and
 *    limitations under the License.
 */
package org.apache.ibatis.mapping;

import java.util.HashMap;
import java.util.List;
import java.util.Map;

import org.apache.ibatis.reflection.MetaObject;
import org.apache.ibatis.reflection.property.PropertyTokenizer;
import org.apache.ibatis.session.Configuration;

/**
 * An actual SQL String got from an {@link SqlSource} after having processed any dynamic content.
 * The SQL may have SQL placeholders "?" and an list (ordered) of an parameter mappings
 * with the additional information for each parameter (at least the property name of the input object to read
 * the value from).
 * <p>
 * Can also have additional parameters that are created by the dynamic language (for loops, bind...).
 *
 * @author Clinton Begin
 */
public class BoundSql {

  private final String sql;
  private final List<ParameterMapping> parameterMappings;
  private final Object parameterObject;
  private final Map<String, Object> additionalParameters;
  private final MetaObject metaParameters;

  public BoundSql(Configuration configuration, String sql, List<ParameterMapping> parameterMappings, Object parameterObject) {
    this.sql = sql;
    this.parameterMappings = parameterMappings;
    this.parameterObject = parameterObject;
    this.additionalParameters = new HashMap<>();
    this.metaParameters = configuration.newMetaObject(additionalParameters);
  }

  public String getSql() {
    return sql;
  }

  public List<ParameterMapping> getParameterMappings() {
    return parameterMappings;
  }

  public Object getParameterObject() {
    return parameterObject;
  }

  public boolean hasAdditionalParameter(String name) {
    String paramName = new PropertyTokenizer(name).getName();
    return additionalParameters.containsKey(paramName);
  }

  public void setAdditionalParameter(String name, Object value) {
    metaParameters.setValue(name, value);
  }

  public Object getAdditionalParameter(String name) {
    return metaParameters.getValue(name);
  }
}
```

打断点 启动调试 可以看出BoundSql 获取到的就是实际的sql语句，以及其中的参数等

##### 5.5 SqlSource

就是构建BoundSql，根据用户传入的参数

```java
/**
 *    Copyright 2009-2019 the original author or authors.
 *
 *    Licensed under the Apache License, Version 2.0 (the "License");
 *    you may not use this file except in compliance with the License.
 *    You may obtain a copy of the License at
 *
 *       http://www.apache.org/licenses/LICENSE-2.0
 *
 *    Unless required by applicable law or agreed to in writing, software
 *    distributed under the License is distributed on an "AS IS" BASIS,
 *    WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
 *    See the License for the specific language governing permissions and
 *    limitations under the License.
 */
package org.apache.ibatis.mapping;

/**
 * Represents the content of a mapped statement read from an XML file or an annotation.
 * It creates the SQL that will be passed to the database out of the input parameter received from the user.
 *
 * @author Clinton Begin
 */
public interface SqlSource {

  BoundSql getBoundSql(Object parameterObject);

}
```

##### 5.6 MappedStatement

```java
public Builder(Configuration configuration, String id, SqlSource sqlSource, SqlCommandType sqlCommandType) {}
```

##### 5.7 StatementHandler

就是对jdbc操作的一个实现

```java
/**
 *    Copyright 2009-2016 the original author or authors.
 *
 *    Licensed under the Apache License, Version 2.0 (the "License");
 *    you may not use this file except in compliance with the License.
 *    You may obtain a copy of the License at
 *
 *       http://www.apache.org/licenses/LICENSE-2.0
 *
 *    Unless required by applicable law or agreed to in writing, software
 *    distributed under the License is distributed on an "AS IS" BASIS,
 *    WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
 *    See the License for the specific language governing permissions and
 *    limitations under the License.
 */
package org.apache.ibatis.executor.statement;

import java.sql.Connection;
import java.sql.SQLException;
import java.sql.Statement;
import java.util.List;

import org.apache.ibatis.cursor.Cursor;
import org.apache.ibatis.executor.parameter.ParameterHandler;
import org.apache.ibatis.mapping.BoundSql;
import org.apache.ibatis.session.ResultHandler;

/**
 * @author Clinton Begin
 */
public interface StatementHandler {

  Statement prepare(Connection connection, Integer transactionTimeout)
      throws SQLException;

  void parameterize(Statement statement)
      throws SQLException;

  void batch(Statement statement)
      throws SQLException;

  int update(Statement statement)
      throws SQLException;

  <E> List<E> query(Statement statement, ResultHandler resultHandler)
      throws SQLException;

  <E> Cursor<E> queryCursor(Statement statement)
      throws SQLException;

  BoundSql getBoundSql();

  ParameterHandler getParameterHandler();

}
```

##### 5.8 Executor

```java
/**
 *    Copyright 2009-2015 the original author or authors.
 *
 *    Licensed under the Apache License, Version 2.0 (the "License");
 *    you may not use this file except in compliance with the License.
 *    You may obtain a copy of the License at
 *
 *       http://www.apache.org/licenses/LICENSE-2.0
 *
 *    Unless required by applicable law or agreed to in writing, software
 *    distributed under the License is distributed on an "AS IS" BASIS,
 *    WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
 *    See the License for the specific language governing permissions and
 *    limitations under the License.
 */
package org.apache.ibatis.executor;

import java.sql.SQLException;
import java.util.List;

import org.apache.ibatis.cache.CacheKey;
import org.apache.ibatis.cursor.Cursor;
import org.apache.ibatis.mapping.BoundSql;
import org.apache.ibatis.mapping.MappedStatement;
import org.apache.ibatis.reflection.MetaObject;
import org.apache.ibatis.session.ResultHandler;
import org.apache.ibatis.session.RowBounds;
import org.apache.ibatis.transaction.Transaction;

/**
 * @author Clinton Begin
 */
public interface Executor {

  ResultHandler NO_RESULT_HANDLER = null;

  int update(MappedStatement ms, Object parameter) throws SQLException;

  <E> List<E> query(MappedStatement ms, Object parameter, RowBounds rowBounds, ResultHandler resultHandler, CacheKey cacheKey, BoundSql boundSql) throws SQLException;

  <E> List<E> query(MappedStatement ms, Object parameter, RowBounds rowBounds, ResultHandler resultHandler) throws SQLException;

  <E> Cursor<E> queryCursor(MappedStatement ms, Object parameter, RowBounds rowBounds) throws SQLException;

  List<BatchResult> flushStatements() throws SQLException;

  void commit(boolean required) throws SQLException;

  void rollback(boolean required) throws SQLException;

  CacheKey createCacheKey(MappedStatement ms, Object parameterObject, RowBounds rowBounds, BoundSql boundSql);

  boolean isCached(MappedStatement ms, CacheKey key);

  void clearLocalCache();

  void deferLoad(MappedStatement ms, MetaObject resultObject, String property, CacheKey key, Class<?> targetType);

  Transaction getTransaction();

  void close(boolean forceRollback);

  boolean isClosed();

  void setExecutorWrapper(Executor executor);

}
```

##### 5.9 SqlSession

定义了对数据库操作的一系列行为

```java
/**
 *    Copyright 2009-2020 the original author or authors.
 *
 *    Licensed under the Apache License, Version 2.0 (the "License");
 *    you may not use this file except in compliance with the License.
 *    You may obtain a copy of the License at
 *
 *       http://www.apache.org/licenses/LICENSE-2.0
 *
 *    Unless required by applicable law or agreed to in writing, software
 *    distributed under the License is distributed on an "AS IS" BASIS,
 *    WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
 *    See the License for the specific language governing permissions and
 *    limitations under the License.
 */
package org.apache.ibatis.session;

import java.io.Closeable;
import java.sql.Connection;
import java.util.List;
import java.util.Map;

import org.apache.ibatis.cursor.Cursor;
import org.apache.ibatis.executor.BatchResult;

/**
 * The primary Java interface for working with MyBatis.
 * Through this interface you can execute commands, get mappers and manage transactions.
 *
 * @author Clinton Begin
 */
public interface SqlSession extends Closeable {

  /**
   * Retrieve a single row mapped from the statement key.
   * @param <T> the returned object type
   * @param statement
   *          the statement
   * @return Mapped object
   */
  <T> T selectOne(String statement);

  /**
   * Retrieve a single row mapped from the statement key and parameter.
   * @param <T> the returned object type
   * @param statement Unique identifier matching the statement to use.
   * @param parameter A parameter object to pass to the statement.
   * @return Mapped object
   */
  <T> T selectOne(String statement, Object parameter);

  /**
   * Retrieve a list of mapped objects from the statement key.
   * @param <E> the returned list element type
   * @param statement Unique identifier matching the statement to use.
   * @return List of mapped object
   */
  <E> List<E> selectList(String statement);

  /**
   * Retrieve a list of mapped objects from the statement key and parameter.
   * @param <E> the returned list element type
   * @param statement Unique identifier matching the statement to use.
   * @param parameter A parameter object to pass to the statement.
   * @return List of mapped object
   */
  <E> List<E> selectList(String statement, Object parameter);

  /**
   * Retrieve a list of mapped objects from the statement key and parameter,
   * within the specified row bounds.
   * @param <E> the returned list element type
   * @param statement Unique identifier matching the statement to use.
   * @param parameter A parameter object to pass to the statement.
   * @param rowBounds  Bounds to limit object retrieval
   * @return List of mapped object
   */
  <E> List<E> selectList(String statement, Object parameter, RowBounds rowBounds);

  /**
   * The selectMap is a special case in that it is designed to convert a list
   * of results into a Map based on one of the properties in the resulting
   * objects.
   * Eg. Return a of Map[Integer,Author] for selectMap("selectAuthors","id")
   * @param <K> the returned Map keys type
   * @param <V> the returned Map values type
   * @param statement Unique identifier matching the statement to use.
   * @param mapKey The property to use as key for each value in the list.
   * @return Map containing key pair data.
   */
  <K, V> Map<K, V> selectMap(String statement, String mapKey);

  /**
   * The selectMap is a special case in that it is designed to convert a list
   * of results into a Map based on one of the properties in the resulting
   * objects.
   * @param <K> the returned Map keys type
   * @param <V> the returned Map values type
   * @param statement Unique identifier matching the statement to use.
   * @param parameter A parameter object to pass to the statement.
   * @param mapKey The property to use as key for each value in the list.
   * @return Map containing key pair data.
   */
  <K, V> Map<K, V> selectMap(String statement, Object parameter, String mapKey);

  /**
   * The selectMap is a special case in that it is designed to convert a list
   * of results into a Map based on one of the properties in the resulting
   * objects.
   * @param <K> the returned Map keys type
   * @param <V> the returned Map values type
   * @param statement Unique identifier matching the statement to use.
   * @param parameter A parameter object to pass to the statement.
   * @param mapKey The property to use as key for each value in the list.
   * @param rowBounds  Bounds to limit object retrieval
   * @return Map containing key pair data.
   */
  <K, V> Map<K, V> selectMap(String statement, Object parameter, String mapKey, RowBounds rowBounds);

  /**
   * A Cursor offers the same results as a List, except it fetches data lazily using an Iterator.
   * @param <T> the returned cursor element type.
   * @param statement Unique identifier matching the statement to use.
   * @return Cursor of mapped objects
   */
  <T> Cursor<T> selectCursor(String statement);

  /**
   * A Cursor offers the same results as a List, except it fetches data lazily using an Iterator.
   * @param <T> the returned cursor element type.
   * @param statement Unique identifier matching the statement to use.
   * @param parameter A parameter object to pass to the statement.
   * @return Cursor of mapped objects
   */
  <T> Cursor<T> selectCursor(String statement, Object parameter);

  /**
   * A Cursor offers the same results as a List, except it fetches data lazily using an Iterator.
   * @param <T> the returned cursor element type.
   * @param statement Unique identifier matching the statement to use.
   * @param parameter A parameter object to pass to the statement.
   * @param rowBounds  Bounds to limit object retrieval
   * @return Cursor of mapped objects
   */
  <T> Cursor<T> selectCursor(String statement, Object parameter, RowBounds rowBounds);

  /**
   * Retrieve a single row mapped from the statement key and parameter
   * using a {@code ResultHandler}.
   * @param statement Unique identifier matching the statement to use.
   * @param parameter A parameter object to pass to the statement.
   * @param handler ResultHandler that will handle each retrieved row
   */
  void select(String statement, Object parameter, ResultHandler handler);

  /**
   * Retrieve a single row mapped from the statement
   * using a {@code ResultHandler}.
   * @param statement Unique identifier matching the statement to use.
   * @param handler ResultHandler that will handle each retrieved row
   */
  void select(String statement, ResultHandler handler);

  /**
   * Retrieve a single row mapped from the statement key and parameter using a {@code ResultHandler} and
   * {@code RowBounds}.
   *
   * @param statement
   *          Unique identifier matching the statement to use.
   * @param parameter
   *          the parameter
   * @param rowBounds
   *          RowBound instance to limit the query results
   * @param handler
   *          ResultHandler that will handle each retrieved row
   */
  void select(String statement, Object parameter, RowBounds rowBounds, ResultHandler handler);

  /**
   * Execute an insert statement.
   * @param statement Unique identifier matching the statement to execute.
   * @return int The number of rows affected by the insert.
   */
  int insert(String statement);

  /**
   * Execute an insert statement with the given parameter object. Any generated
   * autoincrement values or selectKey entries will modify the given parameter
   * object properties. Only the number of rows affected will be returned.
   * @param statement Unique identifier matching the statement to execute.
   * @param parameter A parameter object to pass to the statement.
   * @return int The number of rows affected by the insert.
   */
  int insert(String statement, Object parameter);

  /**
   * Execute an update statement. The number of rows affected will be returned.
   * @param statement Unique identifier matching the statement to execute.
   * @return int The number of rows affected by the update.
   */
  int update(String statement);

  /**
   * Execute an update statement. The number of rows affected will be returned.
   * @param statement Unique identifier matching the statement to execute.
   * @param parameter A parameter object to pass to the statement.
   * @return int The number of rows affected by the update.
   */
  int update(String statement, Object parameter);

  /**
   * Execute a delete statement. The number of rows affected will be returned.
   * @param statement Unique identifier matching the statement to execute.
   * @return int The number of rows affected by the delete.
   */
  int delete(String statement);

  /**
   * Execute a delete statement. The number of rows affected will be returned.
   * @param statement Unique identifier matching the statement to execute.
   * @param parameter A parameter object to pass to the statement.
   * @return int The number of rows affected by the delete.
   */
  int delete(String statement, Object parameter);

  /**
   * Flushes batch statements and commits database connection.
   * Note that database connection will not be committed if no updates/deletes/inserts were called.
   * To force the commit call {@link SqlSession#commit(boolean)}
   */
  void commit();

  /**
   * Flushes batch statements and commits database connection.
   * @param force forces connection commit
   */
  void commit(boolean force);

  /**
   * Discards pending batch statements and rolls database connection back.
   * Note that database connection will not be rolled back if no updates/deletes/inserts were called.
   * To force the rollback call {@link SqlSession#rollback(boolean)}
   */
  void rollback();

  /**
   * Discards pending batch statements and rolls database connection back.
   * Note that database connection will not be rolled back if no updates/deletes/inserts were called.
   * @param force forces connection rollback
   */
  void rollback(boolean force);

  /**
   * Flushes batch statements.
   * @return BatchResult list of updated records
   * @since 3.0.6
   */
  List<BatchResult> flushStatements();

  /**
   * Closes the session.
   */
  @Override
  void close();

  /**
   * Clears local session cache.
   */
  void clearCache();

  /**
   * Retrieves current configuration.
   * @return Configuration
   */
  Configuration getConfiguration();

  /**
   * Retrieves a mapper.
   * @param <T> the mapper type
   * @param type Mapper interface class
   * @return a mapper bound to this SqlSession
   */
  <T> T getMapper(Class<T> type);

  /**
   * Retrieves inner database connection.
   * @return Connection
   */
  Connection getConnection();
}
```
