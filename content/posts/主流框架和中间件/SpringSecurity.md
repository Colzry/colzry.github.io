---
title: "SpringSecurity入门"
description: "SpringSecurity入门"
keywords: "SpringSecurity"

date: 2023-06-16T16:41:30+08:00
lastmod: 2023-06-16T16:41:30+08:00

categories:
  - 框架和中间件
tags:
  - SpringSecurity
---

## 1. 快速入门
### 1.1 创建工程
#### 1.1.1 先创建父工程
![image.png](https://cdn.nlark.com/yuque/0/2022/png/22223333/1660307652271-e1bdf679-a499-4bbb-9482-7f775003cd25.png#clientId=u07cc47ad-e43a-4&crop=0&crop=0&crop=1&crop=1&from=paste&height=711&id=ue18b5275&margin=%5Bobject%20Object%5D&name=image.png&originHeight=1066&originWidth=1182&originalType=binary&ratio=1&rotation=0&showTitle=false&size=85339&status=done&style=none&taskId=ud1ec75d4-d880-471c-9705-2e0f768972a&title=&width=788)
![image.png](https://cdn.nlark.com/yuque/0/2022/png/22223333/1660307770533-e12aa610-1440-4576-82f7-9edff163e9d7.png#clientId=u07cc47ad-e43a-4&crop=0&crop=0&crop=1&crop=1&from=paste&height=711&id=uf4172c51&margin=%5Bobject%20Object%5D&name=image.png&originHeight=1066&originWidth=1182&originalType=binary&ratio=1&rotation=0&showTitle=false&size=73589&status=done&style=none&taskId=ua95d868f-c4df-4617-82d7-17ab2989ae9&title=&width=788)
#### 1.1.2 将不需要的文件都删除
![image.png](https://cdn.nlark.com/yuque/0/2022/png/22223333/1660307911544-530d8557-d0e9-45f8-8cc8-79dbd3065604.png#clientId=u07cc47ad-e43a-4&crop=0&crop=0&crop=1&crop=1&from=paste&height=329&id=u0b46a25e&margin=%5Bobject%20Object%5D&name=image.png&originHeight=494&originWidth=544&originalType=binary&ratio=1&rotation=0&showTitle=false&size=33566&status=done&style=none&taskId=u11cce0f1-068b-4cb5-9442-3383ad84fff&title=&width=362.6666666666667)![image.png](https://cdn.nlark.com/yuque/0/2022/png/22223333/1660307924879-1a2de041-879d-49fd-a9d5-39364f09f5aa.png#clientId=u07cc47ad-e43a-4&crop=0&crop=0&crop=1&crop=1&from=paste&height=248&id=uaadd45dd&margin=%5Bobject%20Object%5D&name=image.png&originHeight=372&originWidth=552&originalType=binary&ratio=1&rotation=0&showTitle=false&size=25918&status=done&style=none&taskId=u2028216a-57d4-4a4a-a144-217fd8747db&title=&width=368)
#### 1.1.3 在该工程中新建Module
![image.png](https://cdn.nlark.com/yuque/0/2022/png/22223333/1660308025221-c74a2f13-facb-4a37-b312-044e2ecbf969.png#clientId=u07cc47ad-e43a-4&crop=0&crop=0&crop=1&crop=1&from=paste&height=218&id=u44163132&margin=%5Bobject%20Object%5D&name=image.png&originHeight=327&originWidth=843&originalType=binary&ratio=1&rotation=0&showTitle=false&size=39090&status=done&style=none&taskId=u2a904923-7b99-46a6-8cd1-c0c69ecb7dd&title=&width=562)
![image.png](https://cdn.nlark.com/yuque/0/2022/png/22223333/1660308099657-09cacb0b-32cb-48cd-a793-b242f21ee0fd.png#clientId=u07cc47ad-e43a-4&crop=0&crop=0&crop=1&crop=1&from=paste&height=610&id=u8479b6a3&margin=%5Bobject%20Object%5D&name=image.png&originHeight=915&originWidth=1182&originalType=binary&ratio=1&rotation=0&showTitle=false&size=77731&status=done&style=none&taskId=u8c340d6a-4978-485b-a128-9bd0ead71b0&title=&width=788)
#### 1.1.4 引入依赖
**父模块依赖**
```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 https://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>2.3.7.RELEASE</version>
        <relativePath/> <!-- lookup parent from repository -->
    </parent>

    <groupId>com.colzry</groupId>
    <artifactId>SpringBootDemo</artifactId>
    <version>0.0.1-SNAPSHOT</version>

    <packaging>pom</packaging>

    <modules>
        <module>SecurityJWT</module>
        <module>AOPDemo</module>
    </modules>

    <properties>
        <maven.compiler.source>8</maven.compiler.source>
        <maven.compiler.target>8</maven.compiler.target>
        <java.version>1.8</java.version>
    </properties>

    <dependencies>
        <!-- 注意：以下引入的依赖在子模块中不必再次引入 -->

        <!-- 引入Spring MVC -->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>

        <!-- 引入热部署依赖 -->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-devtools</artifactId>
            <scope>runtime</scope>
            <optional>true</optional>
        </dependency>

        <!-- 引入配置文件提示依赖 -->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-configuration-processor</artifactId>
            <optional>true</optional>
        </dependency>

        <!-- 引入lombok -->
        <dependency>
            <groupId>org.projectlombok</groupId>
            <artifactId>lombok</artifactId>
            <optional>true</optional>
        </dependency>

        <!-- 引入Springboot JunitTest 测试 -->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
        </dependency>
    </dependencies>


</project>
```
**子模块依赖**
```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <parent>
        <artifactId>SpringBootDemo</artifactId>
        <groupId>com.colzry</groupId>
        <version>0.0.1-SNAPSHOT</version>
    </parent>

    <artifactId>SecurityJWT</artifactId>

    <dependencies>
        <!-- 引入 SpringSecurity-->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-security</artifactId>
        </dependency>

        <!-- 引入mybatis-plus -->
        <dependency>
            <groupId>com.baomidou</groupId>
            <artifactId>mybatis-plus-boot-starter</artifactId>
            <version>3.4.2</version>
        </dependency>

        <!-- 引入mysql连接驱动 -->
        <dependency>
            <groupId>mysql</groupId>
            <artifactId>mysql-connector-java</artifactId>
            <scope>runtime</scope>
        </dependency>

        <!-- 引入druid连接池 -->
        <dependency>
            <groupId>com.alibaba</groupId>
            <artifactId>druid-spring-boot-starter</artifactId>
            <version>1.2.11</version>
        </dependency>

        <!-- 引入redis -->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-data-redis</artifactId>
        </dependency>

        <!-- 引入 apache lang3 工具包 -->
        <dependency>
            <groupId>org.apache.commons</groupId>
            <artifactId>commons-lang3</artifactId>
            <version>3.12.0</version>
        </dependency>

        <!-- 引入JWT token 验证 -->
        <dependency>
            <groupId>io.jsonwebtoken</groupId>
            <artifactId>jjwt</artifactId>
            <version>0.9.1</version>
        </dependency>

        <!-- 引入阿里 fastJson -->
        <dependency>
            <groupId>com.alibaba.fastjson2</groupId>
            <artifactId>fastjson2</artifactId>
            <version>2.0.11</version>
        </dependency>
        <!-- 引入阿里 fastJson 扩展 -->
        <dependency>
            <groupId>com.alibaba.fastjson2</groupId>
            <artifactId>fastjson2-extension</artifactId>
            <version>2.0.11</version>
        </dependency>

    </dependencies>

    <build>
        <plugins>
            <plugin>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-maven-plugin</artifactId>
                <configuration>
                    <mainClass>com.securityJWT.SecurityJWTApplication</mainClass>
                    <layout>JAR</layout>
                </configuration>
            </plugin>
        </plugins>
    </build>

</project>
```
#### 1.1.5 初始化子模块项目
![image.png](https://cdn.nlark.com/yuque/0/2022/png/22223333/1660308711681-1046829d-b07d-42df-b65d-b65b6cf7bccd.png#clientId=u07cc47ad-e43a-4&crop=0&crop=0&crop=1&crop=1&from=paste&height=324&id=ubd756a9d&margin=%5Bobject%20Object%5D&name=image.png&originHeight=486&originWidth=429&originalType=binary&ratio=1&rotation=0&showTitle=false&size=24707&status=done&style=none&taskId=u8d748151-2f17-4eb9-bebd-dc92f762450&title=&width=286)
```java
@SpringBootApplication
public class SecurityJWTApplication {
    public static void main(String[] args) {
        SpringApplication.run(SecurityJWTApplication.class, args);
    }
}
```
```yaml
server:
  port: 8010

spring:
  datasource:
    driver-class-name: com.mysql.cj.jdbc.Driver
    # 使用druid数据源
    type: com.alibaba.druid.pool.DruidDataSource
    url: jdbc:mysql://43.142.174.216:5206/demo?useUnicode=true&characterEncoding=UTF-8&useSSL=false&serverTimezone=Asia/Shanghai&allowPublicKeyRetrieval=true
    username: mysql
    password: mysql
  redis:
    host: 43.142.174.216
    port: 5079
    password: czyadmin


mybatis-plus:
  configuration:
    # 是否开启自动驼峰命名规则映射:从数据库列名到Java属性驼峰命名的类似映射
    map-underscore-to-camel-case: true
    # 如果查询结果中包含空值的列，则 MyBatis 在映射的时候，不会映射这个字段
    call-setters-on-nulls: true
    # Mybatis-Plus SQL 日志打印
    log-impl: org.apache.ibatis.logging.stdout.StdOutImpl
```
### 1.2 测试SpringSecurity
新建一个Controller来测试
```java
@RestController
@RequestMapping("/hello")
public class TestController {

    @GetMapping("{name}")
    public String getHello(@PathVariable String name) {
        return "Hello " + name;
    }
}
```
启动后密码会初始化
![image.png](https://cdn.nlark.com/yuque/0/2022/png/22223333/1660309134479-8d6d8177-a534-4fdd-a9a0-710210ff1a31.png#clientId=u07cc47ad-e43a-4&crop=0&crop=0&crop=1&crop=1&from=paste&height=345&id=u330960ef&margin=%5Bobject%20Object%5D&name=image.png&originHeight=517&originWidth=1638&originalType=binary&ratio=1&rotation=0&showTitle=false&size=132086&status=done&style=none&taskId=u3f22d911-f5ba-451d-b818-7e537d362bd&title=&width=1092)
访问**/hello/Clozry**会被拦截
![image.png](https://cdn.nlark.com/yuque/0/2022/png/22223333/1660309279846-1ed55b04-88c8-449c-ab4d-38a166848063.png#clientId=u07cc47ad-e43a-4&crop=0&crop=0&crop=1&crop=1&from=paste&height=345&id=u69362e94&margin=%5Bobject%20Object%5D&name=image.png&originHeight=518&originWidth=1374&originalType=binary&ratio=1&rotation=0&showTitle=false&size=34332&status=done&style=none&taskId=u153e7813-dd9c-4e45-bca2-07bed31c7ca&title=&width=916)
输入用户名**user**和**控制台打印的密码**后可以正常访问
![image.png](https://cdn.nlark.com/yuque/0/2022/png/22223333/1660309442326-428c52b1-541d-4e71-baac-40415cd8d6f7.png#clientId=u07cc47ad-e43a-4&crop=0&crop=0&crop=1&crop=1&from=paste&height=117&id=u112f0eef&margin=%5Bobject%20Object%5D&name=image.png&originHeight=175&originWidth=502&originalType=binary&ratio=1&rotation=0&showTitle=false&size=15586&status=done&style=none&taskId=u310ab099-1f5e-4bbc-b125-ca09ec5dda8&title=&width=334.6666666666667)
## 2. 认证
### 2.1 登录校验流程
![image.png](https://cdn.nlark.com/yuque/0/2022/png/22223333/1660311785651-9beb0cf7-a1e4-4353-8e93-75fe76c1ad7a.png#clientId=u07cc47ad-e43a-4&crop=0&crop=0&crop=1&crop=1&from=paste&height=685&id=u9a6c9749&margin=%5Bobject%20Object%5D&name=image.png&originHeight=1028&originWidth=1091&originalType=binary&ratio=1&rotation=0&showTitle=false&size=1770248&status=done&style=none&taskId=u0032c163-28c8-403a-b45b-e187f2d05f6&title=&width=727.3333333333334)
### 2.2 SpringSecurity流程
SpringSecurity的原理其实就是一个过滤器链，内部包含了提供各种功能的过滤器。这里我们可以看看入门案例中的过滤器。
![image.png](https://cdn.nlark.com/yuque/0/2022/png/22223333/1660310326325-aecf2805-b522-4e89-ae18-a64b6fcddcaf.png#clientId=u07cc47ad-e43a-4&crop=0&crop=0&crop=1&crop=1&from=paste&height=289&id=ua4ae8187&margin=%5Bobject%20Object%5D&name=image.png&originHeight=434&originWidth=1191&originalType=binary&ratio=1&rotation=0&showTitle=false&size=406510&status=done&style=none&taskId=u96d931b3-99f9-45ab-ad04-ba1eae118c6&title=&width=794)
图中只展示了核心过滤器，其它的非核心过滤器并没有在图中展示。

- UsernamePasswordAuthenticationFilter:负责处理我们在登陆页面填写了用户名密码后的登陆请求。入门案例的认证工作主要有它负责。
- ExceptionTranslationFilter：处理过滤器链中抛出的任何AccessDeniedException和AuthenticationException。
- FilterSecurityInterceptor：负责权限校验的过滤器。
### 2.3 认证流程
![image.png](https://cdn.nlark.com/yuque/0/2022/png/22223333/1660316114060-49e728f1-b864-4e77-bf5d-7800e5a8a8f5.png#clientId=u07cc47ad-e43a-4&crop=0&crop=0&crop=1&crop=1&from=paste&height=515&id=ud4b2a3e8&margin=%5Bobject%20Object%5D&name=image.png&originHeight=773&originWidth=1802&originalType=binary&ratio=1&rotation=0&showTitle=false&size=369068&status=done&style=none&taskId=ufbb7acbd-b2c9-408f-9881-af59ca06b30&title=&width=1201.3333333333333)

- Authentication接口: 它的实现类，表示当前访问系统的用户，封装了用户相关信息。
- AuthenticationManager接口：定义了认证Authentication的方法
- UserDetailsService接口：加载用户特定数据的核心接口。里面定义了一个根据用户名查询用户信息的方法。
- UserDetails接口：提供核心用户信息。通过UserDetailsService根据用户名获取处理的用户信息要封装成UserDetails对象返回。然后将这些信息封装到Authentication对象中。

### 2.4 编写认证代码
#### 2.4.1 思路分析
登录
①自定义登录接口
调用ProviderManager的方法进行认证 如果认证通过生成jwt
把用户信息存入redis中
②自定义UserDetailsService
在这个实现类中去查询数据库
校验：
①定义Jwt认证过滤器
获取token
解析token获取其中的userid
从redis中获取用户信息
存入SecurityContextHolder
#### 2.4.2 准备工作

1. 引入_1.1.4_中的依赖
2. 添加Redis配置和RedisUtils(简化版)
```java
@Configuration
@EnableCaching // spring版本3.1起自动缓存 由CacheManager Bean配置
public class RedisConfig extends CachingConfigurerSupport {
    @Bean
    @SuppressWarnings(value = {"unchecked", "rawtypes"})
    public RedisTemplate<Object, Object> redisTemplate(RedisConnectionFactory connectionFactory) {
        RedisTemplate<Object, Object> template = new RedisTemplate<>();
        template.setConnectionFactory(connectionFactory);

        // 需要添加 fastjson2-extension 依赖, 使用fastJson2来序列化
        GenericFastJsonRedisSerializer serializer = new GenericFastJsonRedisSerializer();

        template.setDefaultSerializer(serializer);
        template.setValueSerializer(serializer);
        // 使用StringRedisSerializer来序列化和反序列化redis的key值
        template.setKeySerializer(new StringRedisSerializer());
        template.setHashKeySerializer(new StringRedisSerializer());
        template.setHashValueSerializer(new StringRedisSerializer());
        template.afterPropertiesSet();
        return template;
    }

    @Bean
    public CacheManager cacheManager(RedisConnectionFactory connectionFactory) {
        // 缓存30分钟
        Duration duration = Duration.ofSeconds(30);
        RedisCacheConfiguration redisCacheConfiguration = RedisCacheConfiguration.defaultCacheConfig().entryTtl(duration);
        return RedisCacheManager.builder(RedisCacheWriter.nonLockingRedisCacheWriter(connectionFactory))
                .cacheDefaults(redisCacheConfiguration).build();
    }
}

```
```java
@Component
public class RedisUtil {

    @Autowired
    private RedisTemplate<Object, Object> redisTemplate;

    /**
     * 判断 key 是否存在
     *
     * @param key 键
     * @return 如果存在 key 则返回 true，否则返回 false
     */
    public Boolean exists(String key) {
        return redisTemplate.hasKey(key);
    }


    /**
     * 获取 Key 的类型
     *
     * @param key 键
     */
    public String type(String key) {
        DataType dataType = redisTemplate.type(key);
        assert dataType != null;
        return dataType.code();
    }

    /**
     * 指定缓存失效时间
     *
     * @param key  键
     * @param time 时间(秒)
     * @return 30
     */
    public boolean expire(String key, long time) {
        try {
            if (time > 0) {
                redisTemplate.expire(key, time, TimeUnit.SECONDS);
            }
            return true;
        } catch (Exception e) {
            e.printStackTrace();
            return false;
        }
    }

    /**
     * 根据key 获取过期时间
     *
     * @param key 键 不能为null
     * @return 时间(秒) 返回0代表为永久有效
     */
    public long getExpire(String key) {

        return redisTemplate.getExpire(key, TimeUnit.SECONDS);

    }

    /**
     * 判断key是否存在
     *
     * @param key 键
     * @return true 存在 false不存在
     */
    public boolean hasKey(String key) {
        try {
            return redisTemplate.hasKey(key);
        } catch (Exception e) {
            e.printStackTrace();
            return false;
        }
    }

    /**
     * 删除 key
     *
     * @param key 键
     */
    public Long delete(String... key) {
        if (key == null || key.length < 1) {
            return 0L;
        }
        return redisTemplate.delete(Arrays.asList(key));
    }

    /**
     * 获取所有的keys
     *
     * @return
     */
    public Set<Object> keys() {
        Set<Object> keys1 = redisTemplate.keys("*");

        return keys1;
    }

    /**
     * 获取所有的keys得所有的值
     *
     * @param keys
     * @return
     */
    public HashMap<Object, Object> getKeysValue(String keys) {

        Set<Object> keys1 = redisTemplate.keys("*");

        HashMap<Object, Object> hashMap = new HashMap<Object, Object>();
        for (Object s : keys1) {
            Object o = redisTemplate.opsForValue().get(keys + s);
            System.out.println("o=" + o);
            hashMap.put(keys1, o);
        }
        return hashMap;
    }

    /**
     * 删除缓存
     *
     * @param key 可以传一个值 或多个
     */
    public void del(String... key) {
        if (key != null && key.length > 0) {
            if (key.length == 1) {
                redisTemplate.delete(key[0]);
            } else {

                redisTemplate.delete(CollectionUtils.arrayToList(key));
            }

        }

    }

    // ============================String=============================

    /**
     * 普通缓存获取
     *
     * @param key 键
     * @return 值
     */
    public Object get(String key) {
        return key == null ? null : redisTemplate.opsForValue().get(key);
    }

    /**
     * 普通缓存放入
     *
     * @param key   键
     * @param value 值
     * @return true成功 false失败
     */
    public boolean set(String key, Object value) {
        try {
            redisTemplate.opsForValue().set(key, value);
            return true;
        } catch (Exception e) {
            e.printStackTrace();
            return false;
        }
    }

    /**
     * 普通缓存放入并设置时间
     *
     * @param key   键
     * @param value 值
     * @param time  时间(秒) time要大于0 如果time小于等于0 将设置无限期
     * @return true成功 false 失败
     */
    public boolean set(String key, Object value, long time) {
        try {
            if (time > 0) {
                redisTemplate.opsForValue().set(key, value, time, TimeUnit.SECONDS);
            } else {
                set(key, value);
            }
            return true;
        } catch (Exception e) {
            e.printStackTrace();
            return false;
        }
    }

    /**
     * 递增
     *
     * @param key   键
     * @param delta 要增加几(大于0)
     * @return
     */
    public long incr(String key, long delta) {
        if (delta < 0) {
            throw new RuntimeException("递增因子必须大于0");
        }
        return redisTemplate.opsForValue().increment(key, delta);
    }

    /**
     * 递减
     *
     * @param key   键
     * @param delta 要减少几(小于0)
     * @return
     */
    public long decr(String key, long delta) {
        if (delta < 0) {
            throw new RuntimeException("递减因子必须大于0");
        }
        return redisTemplate.opsForValue().increment(key, -delta);
    }
    
}
```

3. 添加响应类Result
```java
@Data
@AllArgsConstructor
public class Result {

    private boolean success;

    private int code;

    private String msg;

    private Object data;

    public Result(boolean success, int code, String msg) {
        this.success = success;
        this.code = code;
        this.msg = msg;
    }


    public static Result success() {
        return new Result(true, 200, "success");
    }

    public static Result success(Object data) {
        return new Result(true, 200, "success", data);
    }

    public static Result fail(String msg) {
        return new Result(false, 500, msg, null);
    }

    public static Result fail(int code, String msg) {
        return new Result(false, code, msg, null);
    }
}

```

4. Jwt工具类
```java
public class JwtUtil {

    //有效期为
    public static final Long JWT_TTL = 60 * 60 * 1000L;// 60 * 60 * 1000  一个小时
    //设置秘钥明文
    public static final String JWT_KEY = "colzry";

    public static String getUUID(){
        String token = UUID.randomUUID().toString().replaceAll("-", "");
        return token;
    }

    /**
     * 生成jtw
     * @param subject token中要存放的数据（json格式）
     * @return
     */
    public static String createJWT(String subject) {
        JwtBuilder builder = getJwtBuilder(subject, null, getUUID());// 设置过期时间
        return builder.compact();
    }

    /**
     * 生成jtw
     * @param subject token中要存放的数据（json格式）
     * @param ttlMillis token超时时间
     * @return
     */
    public static String createJWT(String subject, Long ttlMillis) {
        JwtBuilder builder = getJwtBuilder(subject, ttlMillis, getUUID());// 设置过期时间
        return builder.compact();
    }

    private static JwtBuilder getJwtBuilder(String subject, Long ttlMillis, String uuid) {
        SignatureAlgorithm signatureAlgorithm = SignatureAlgorithm.HS256;
        SecretKey secretKey = generalKey();
        long nowMillis = System.currentTimeMillis();
        Date now = new Date(nowMillis);
        if(ttlMillis==null){
            ttlMillis=JwtUtil.JWT_TTL;
        }
        long expMillis = nowMillis + ttlMillis;
        Date expDate = new Date(expMillis);
        return Jwts.builder()
                .setId(uuid)              //唯一的ID
                .setSubject(subject)   // 主题  可以是JSON数据
                .setIssuer("colzry")     // 签发者
                .setIssuedAt(now)      // 签发时间
                .signWith(signatureAlgorithm, secretKey) //使用HS256对称加密算法签名, 第二个参数为秘钥
                .setExpiration(expDate);
    }

    /**
     * 创建token
     * @param id
     * @param subject
     * @param ttlMillis
     * @return
     */
    public static String createJWT(String id, String subject, Long ttlMillis) {
        JwtBuilder builder = getJwtBuilder(subject, ttlMillis, id);// 设置过期时间
        return builder.compact();
    }


    /**
     * 生成加密后的秘钥 secretKey
     * @return
     */
    public static SecretKey generalKey() {
        byte[] encodedKey = Base64.getDecoder().decode(JwtUtil.JWT_KEY);
        SecretKey key = new SecretKeySpec(encodedKey, 0, encodedKey.length, "AES");
        return key;
    }

    /**
     * 解析
     *
     * @param jwt
     * @return
     * @throws Exception
     */
    public static Claims parseJWT(String jwt) throws Exception {
        SecretKey secretKey = generalKey();
        return Jwts.parser()
                .setSigningKey(secretKey)
                .parseClaimsJws(jwt)
                .getBody();
    }

    public static void main(String[] args) throws Exception {
        String token = "eyJhbGciOiJIUzI1NiJ9.eyJqdGkiOiJjYWM2ZDVhZi1mNjVlLTQ0MDAtYjcxMi0zYWEwOGIyOTIwYjQiLCJzdWIiOiJzZyIsImlzcyI6InNnIiwiaWF0IjoxNjM4MTA2NzEyLCJleHAiOjE2MzgxMTAzMTJ9.JVsSbkP94wuczb4QryQbAke3ysBDIL5ou8fWsbt_ebg";
        Claims claims = parseJWT(token);
        System.out.println(claims);
    }
}
```

5. 用户数据库表和用户实体类
```sql
CREATE TABLE `sys_user` (
  `id` BIGINT(20) NOT NULL AUTO_INCREMENT COMMENT '主键',
  `user_name` VARCHAR(64) NOT NULL DEFAULT 'NULL' COMMENT '用户名',
  `nick_name` VARCHAR(64) NOT NULL DEFAULT 'NULL' COMMENT '昵称',
  `password` VARCHAR(64) NOT NULL DEFAULT 'NULL' COMMENT '密码',
  `status` CHAR(1) DEFAULT '0' COMMENT '账号状态（0正常 1停用）',
  `email` VARCHAR(64) DEFAULT NULL COMMENT '邮箱',
  `phone_number` VARCHAR(32) DEFAULT NULL COMMENT '手机号',
  `gender` CHAR(1) DEFAULT NULL COMMENT '用户性别（0女，1男，2未知）',
  `avatar` VARCHAR(128) DEFAULT NULL COMMENT '头像',
  `user_type` CHAR(1) NOT NULL DEFAULT '1' COMMENT '用户类型（0管理员，1普通用户）',
  `create_by` BIGINT(20) DEFAULT NULL COMMENT '创建人的用户id',
  `create_time` DATETIME DEFAULT NULL COMMENT '创建时间',
  `update_by` BIGINT(20) DEFAULT NULL COMMENT '更新人',
  `update_time` DATETIME DEFAULT NULL COMMENT '更新时间',
  `del_flag` INT(11) DEFAULT '0' COMMENT '删除标志（0代表未删除，1代表已删除）',
  PRIMARY KEY (`id`)
) ENGINE=INNODB AUTO_INCREMENT=2 DEFAULT CHARSET=utf8mb4 COMMENT='用户表'


--密码原文存储
insert into sys_user(user_name,nick_name,password,status,email,phone_number,gender,avatar,user_type,create_by,create_time,update_by,update_time,del_flag) 
values('colzry','Colzry','{noop}123456','0',null,'colzry@163.com','男','','0','0','2022-08-01','0','2022-08-02','0')
```
```java
@Data
@AllArgsConstructor
@NoArgsConstructor
@TableName(value = "sys_user")
public class User implements Serializable {
    private static final long serialVersionUID = -40356785423868312L;

    /**
     * 主键
     */
    @TableId
    private Long id;
    /**
     * 用户名
     */
    private String userName;
    /**
     * 昵称
     */
    private String nickName;
    /**
     * 密码
     */
    private String password;
    /**
     * 账号状态（0正常 1停用）
     */
    private String status;
    /**
     * 邮箱
     */
    private String email;
    /**
     * 手机号
     */
    private String phoneNumber;
    /**
     * 用户性别（0女，1男，2未知）
     */
    private String gender;
    /**
     * 头像
     */
    private String avatar;
    /**
     * 用户类型（0管理员，1普通用户）
     */
    private String userType;
    /**
     * 创建人的用户id
     */
    private Long createBy;
    /**
     * 创建时间
     */
    private Date createTime;
    /**
     * 更新人
     */
    private Long updateBy;
    /**
     * 更新时间
     */
    private Date updateTime;
    /**
     * 删除标志（0代表未删除，1代表已删除）
     */
    private Integer delFlag;
}
```
#### 2.4.3 实现
##### 2.4.3.1 实现UserDetailsService和封装UserDetails

1. 创建User对应的Mapper和Service
```java
@Mapper
public interface UserMapper extends BaseMapper<User> {
}
```
```java
@Service
public interface UserService extends IService<User> {
}
```
```java
@Service
public class UserServiceImpl extends ServiceImpl<UserMapper, User> {
}
```

2. 实现UserDetailsService

从之前的分析我们可以知道，我们可以实现一个UserDetailsService,让SpringSecurity使用我们的UserDetailsService。我们自己的UserDetailsService可以从数据库中查询用户名和密码。
```java
@Service
public class UserDetailsServiceImpl implements UserDetailsService {

    @Autowired
    private UserMapper userMapper;

    @Override
    public UserDetails loadUserByUsername(String username) throws UsernameNotFoundException {
        //根据用户名查询用户信息
        LambdaQueryWrapper<User> wrapper = new LambdaQueryWrapper<>();
        wrapper.eq(User::getUserName, username);
        User user = userMapper.selectOne(wrapper);
        //如果查询不到数据就通过抛出异常来给出提示
        if (Objects.isNull(user)) {
            throw new RuntimeException("用户名或密码错误");
        }
        //TODO 根据用户查询权限信息 添加到LoginUser中

        //封装成UserDetails对象返回 
        return new LoginUser(user);
    }
}
```

3. 封装查询用户

因为UserDetailsService方法的返回值是UserDetails类型，所以需要定义一个类，实现该接口，把用户信息封装在其中。
```java
@Data
@NoArgsConstructor
@AllArgsConstructor
public class LoginUser implements UserDetails {

    private User user;


    @Override
    public Collection<? extends GrantedAuthority> getAuthorities() {
        return null;
    }

    @Override
    public String getPassword() {
        return user.getPassword();
    }

    @Override
    public String getUsername() {
        return user.getUserName();
    }

    @Override
    public boolean isAccountNonExpired() {
        return true;
    }

    @Override
    public boolean isAccountNonLocked() {
        return true;
    }

    @Override
    public boolean isCredentialsNonExpired() {
        return true;
    }

    @Override
    public boolean isEnabled() {
        return true;
    }
}
```
注意：如果要测试，需要往用户表中写入用户数据，并且如果你想让用户的密码是明文存储，需要在密码前加{noop}。例如
![image.png](https://cdn.nlark.com/yuque/0/2022/png/22223333/1660320316201-61df9a52-e3fa-4df2-89f3-3a773232acc5.png#clientId=u07cc47ad-e43a-4&crop=0&crop=0&crop=1&crop=1&from=paste&height=44&id=u84552a2f&margin=%5Bobject%20Object%5D&name=image.png&originHeight=66&originWidth=830&originalType=binary&ratio=1&rotation=0&showTitle=false&size=6214&status=done&style=none&taskId=uc469b082-6ae9-4b3d-af90-c917255cacc&title=&width=553.3333333333334)
##### 2.4.3.2 配置密码加密
我们只需要使用把BCryptPasswordEncoder对象注入Spring容器中，SpringSecurity就会使用该PasswordEncoder来进行密码校验，注意：不要去重写auth配置方法，不然需要在里面重新配置密码加密方式
```java
@Configuration
public class SecurityConfig extends WebSecurityConfigurerAdapter {

    /**
     * 不要重写该方法，不然需要重新设置密码加密方式
    @Override
    public void configure(AuthenticationManagerBuilder auth) throws Exception {
        // 设置密码加密方式，验证密码的在这里
        auth.userDetailsService(userDetailsService).passwordEncoder(passwordEncoder());
    }
    */
    
    @Bean
    public PasswordEncoder passwordEncoder(){
        return new BCryptPasswordEncoder();
    }

}
```
编写密码测试，生成密文密码，替换掉数据库中的明文密码
```java
@SpringBootTest(classes = {SecurityJWTApplication.class})
public class MapperTest {
    
    @Autowired
    private UserMapper userMapper;

    @Autowired
    private PasswordEncoder passwordEncoder;
    
    @Test
    public void testPassword() {
        String encode = passwordEncoder.encode("123456");
        System.out.println(encode);

        LambdaQueryWrapper<User> queryWrapper = new LambdaQueryWrapper<>();
        // 查找替换掉数据库中的colzry用户密码
        queryWrapper.eq(User::getUserName, "colzry");
        User user = new User();
        user.setPassword(encode);
        userMapper.update(user, queryWrapper);
    }
}
```
##### 2.4.3.3 编写登录接口
接下我们需要自定义登陆接口，然后让SpringSecurity对这个接口放行,让用户访问这个接口的时候不用登录也能访问。
```java
@RestController
@RequestMapping("/user")
public class LoginController {

    @Autowired
    private LoginServiceImpl loginService;

    @PostMapping("/login")
    public Result login(@RequestBody User user) {
        return loginService.login(user);
    }

    @GetMapping("/logout")
    public Result logout() {
        return loginService.logout();
    }
}
```
在接口中我们通过AuthenticationManager的authenticate方法来进行用户认证,所以需要在SecurityConfig配置文件中把AuthenticationManager注入容器。
```java
@Configuration
public class WebSecurityConfig extends WebSecurityConfigurerAdapter {
    
    @Autowired
    private JwtAuthenticationTokenFilter jwtAuthenticationTokenFilter;


    @Override
    protected void configure(HttpSecurity http) throws Exception {
        http
                //关闭csrf
                .csrf().disable()
                //不通过Session获取SecurityContext
                .sessionManagement().sessionCreationPolicy(SessionCreationPolicy.STATELESS)
                .and()
                .authorizeRequests()
                // 对于登录接口 允许匿名访问
                .antMatchers("/user/login").anonymous()
                // 除上面外的所有请求全部需要鉴权认证
                .anyRequest().authenticated();

        //把token校验过滤器添加到过滤器链中
        http.addFilterBefore(jwtAuthenticationTokenFilter, UsernamePasswordAuthenticationFilter.class);
    }
    

    /**
     * 添加密码加密方式到IOC中
     */
    @Bean
    public PasswordEncoder passwordEncoder() {
        // 使用BCrypt加密密码
        return new BCryptPasswordEncoder();
    }

    @Bean
    @Override
    public AuthenticationManager authenticationManagerBean() throws Exception {
        return super.authenticationManagerBean();
    }
}
```
认证成功的话要生成一个jwt，放入响应中返回。并且为了让用户下回请求时能通过jwt识别出具体的是哪个用户，我们需要把用户信息存入redis，可以把用户id作为key。
```java
@Service
@Slf4j
public class LoginServiceImpl implements LoginService {

    @Autowired
    private AuthenticationManager authenticationManager;

    @Autowired
    private RedisUtil redisUtil;


    @Override
    public Result login(User user) {
        // AuthenticationManager authenticate进行用户认证
        UsernamePasswordAuthenticationToken authenticationToken =
                new UsernamePasswordAuthenticationToken(user.getUserName(), user.getPassword());
        Authentication authenticate = authenticationManager.authenticate(authenticationToken);
        log.info("------AU---------{}", authenticate);
        // 认证没有通过
        if (Objects.isNull(authenticate)) {
            throw new RuntimeException("用户名或密码错误");
        }

        // 认证成功，使用user_id生成token
        LoginUser loginUser =  (LoginUser) authenticate.getPrincipal();
        String userId = loginUser.getUser().getId().toString();
        String jwt = JwtUtil.createJWT(userId);

        // 把authentication存入redis
        redisUtil.set("login:" + userId, loginUser);

        // 把token响应给前端
        HashMap<String, String> map = new HashMap<>();
        map.put("token", jwt);
        return Result.success(map);
    }

    @Override
    public Result logout() {
        Authentication authentication = SecurityContextHolder.getContext().getAuthentication();
        LoginUser loginUser = (LoginUser) authentication.getPrincipal();
        Long userId = loginUser.getUser().getId();
        redisUtil.del("login:" + userId);
        return Result.success("退出成功");
    }

}
```
##### 2.4.3.4 添加认证过滤器
已经在WebSecurityConfig中添加配置，配置代码如下
```java
//把token校验过滤器添加到过滤器链中
http.addFilterBefore(jwtAuthenticationTokenFilter, UsernamePasswordAuthenticationFilter.class);
```
```java
@Component
public class JwtAuthenticationTokenFilter extends OncePerRequestFilter {

    @Autowired
    private RedisUtil redisUtil;

    @Override
    protected void doFilterInternal(HttpServletRequest request, HttpServletResponse response, FilterChain filterChain) throws ServletException, IOException {
        // 获取token
        String token = request.getHeader("token");
        if (StringUtils.isEmpty(token)) {
            // 放行，让它被SpringSecurity拦截
            filterChain.doFilter(request, response);
            return;
        }

        // 解析token
        String userId;
        try {
            Claims claims = JwtUtil.parseJWT(token);
            userId = claims.getSubject();
        } catch (Exception e) {
            e.printStackTrace();
            throw new RuntimeException("token非法");
        }

        // 从redis中获取用户信息
        String redisKey = "login:" + userId;
        LoginUser loginUser = (LoginUser) redisUtil.get(redisKey);
        if (Objects.isNull(loginUser)) {
            throw new RuntimeException("用户未登录");
        }

        //存入SecurityContextHolder
        //TODO 获取权限信息封装到Authentication中
        UsernamePasswordAuthenticationToken authenticationToken =
                new UsernamePasswordAuthenticationToken(loginUser,null,null);
        //认证通过，将用户信息存入SecurityContextHolder
        SecurityContextHolder.getContext().setAuthentication(authenticationToken);
        //放行
        filterChain.doFilter(request, response);

    }
}
```
##### 2.4.3.5 演示效果
> 登录

![image.png](https://cdn.nlark.com/yuque/0/2022/png/22223333/1660388163694-0cf8d59e-573d-4e02-82ad-3b5f9b2a8e34.png#clientId=u07cc47ad-e43a-4&crop=0&crop=0&crop=1&crop=1&from=paste&height=488&id=uaf2d28f8&margin=%5Bobject%20Object%5D&name=image.png&originHeight=732&originWidth=1417&originalType=binary&ratio=1&rotation=0&showTitle=false&size=88965&status=done&style=none&taskId=u485a1ccd-7ff7-4cb6-b07d-91a134bce8e&title=&width=944.6666666666666)
> 携带token访问


> 登出

![image.png](https://cdn.nlark.com/yuque/0/2022/png/22223333/1660388282333-350e59aa-4e51-41c8-9c3e-1fb7021e5a0b.png#clientId=u07cc47ad-e43a-4&crop=0&crop=0&crop=1&crop=1&from=paste&height=499&id=u5f770c82&margin=%5Bobject%20Object%5D&name=image.png&originHeight=749&originWidth=1421&originalType=binary&ratio=1&rotation=0&showTitle=false&size=84253&status=done&style=none&taskId=u2f30a83c-cb14-4551-8057-95ee8d3aa42&title=&width=947.3333333333334)

## 3. 授权
### 3.1 RBAC权限模型
在RBAC模型里面，有3个基础组成部分，分别是：**用户**、**角色**和**权限**
RBAC通过定义角色的权限，并对用户授予某个角色从而来控制用户的权限，实现了用户和权限的逻辑分离
![image.png](https://cdn.nlark.com/yuque/0/2022/png/22223333/1660651762625-0f724ec1-af7e-481b-929d-fa6ebc5894dc.png#clientId=u376b5a7a-5be9-4&crop=0&crop=0&crop=1&crop=1&from=paste&height=131&id=ud4c2fc43&margin=%5Bobject%20Object%5D&name=image.png&originHeight=197&originWidth=829&originalType=binary&ratio=1&rotation=0&showTitle=false&size=68809&status=done&style=none&taskId=u7da21edc-91ba-4e35-93af-b0cf93e20d7&title=&width=552.6666666666666)
**将权限颗粒化，绑定给不同的角色，从而将角色授权给用户**
例如图书管理系统
![image.png](https://cdn.nlark.com/yuque/0/2022/png/22223333/1660652496584-5d06cef2-a903-48ce-9933-d8f84b097b5e.png#clientId=u376b5a7a-5be9-4&crop=0&crop=0&crop=1&crop=1&from=paste&height=367&id=u135ded24&margin=%5Bobject%20Object%5D&name=image.png&originHeight=550&originWidth=1030&originalType=binary&ratio=1&rotation=0&showTitle=false&size=307702&status=done&style=none&taskId=u3d6c252b-f39d-44c2-b890-67c46c07d9a&title=&width=686.6666666666666)
RBAC支持三个著名的安全原则：最小权限原则、责任分离原则和数据抽象原则

- 最小权限原则：RBAC可以将角色配置成其完成任务所需的最小权限集合
- 责任分离原则：可以通过调用相互独立互斥的角色来共同完成敏感的任务，例如要求一个计账员和财务管理员共同参与统一过账操作
- 数据抽象原则：可以通过权限的抽象来体现，例如财务操作用借款、存款等抽象权限，而不是使用典型的读、写、执行权限
### 3.2 授权实现
#### 3.2.1 限制访问资源的权限
SpringSecurity为我们提供了基于注解的权限控制方案，这也是我们项目中主要采用的方式。我们可以使用注解去指定访问对应的资源所需的权限。
但是要使用它我们需要先开启相关配置springSecurity里面添加
```java
@EnableGlobalMethodSecurity(prePostEnabled = true)
```
![image.png](https://cdn.nlark.com/yuque/0/2022/png/22223333/1660652825078-6e8f9889-64fb-4cc5-90c2-ec618405aa7d.png#clientId=u376b5a7a-5be9-4&crop=0&crop=0&crop=1&crop=1&from=paste&height=92&id=u91f42dda&margin=%5Bobject%20Object%5D&name=image.png&originHeight=138&originWidth=1037&originalType=binary&ratio=1&rotation=0&showTitle=false&size=25098&status=done&style=none&taskId=u34c13cae-666b-475b-89ea-faaaf7a202b&title=&width=691.3333333333334)
#### 3.2.2 封装权限信息

1. 之前定义了UserDetails的实现类LoginUser，想要让其能封装权限信息就要对其进行修改
```java
@Data
@NoArgsConstructor
@AllArgsConstructor
public class LoginUser implements UserDetails {

    private User user;

    //存储权限信息
    private List<String> permissions;

    //存储SpringSecurity所需要的权限信息的集合
    @JSONField(serialize = false, deserialize = false) // 不对此字段序列化和反序列化
    private List<SimpleGrantedAuthority> authorities;

    public LoginUser(User user,List<String> permissions) {
        this.user = user;
        this.permissions = permissions;
    }

    @Override
    public Collection<? extends GrantedAuthority> getAuthorities() {
        if(authorities!=null){
            return authorities;
        }
        //把permissions中字符串类型的权限信息转换成SimpleGrantedAuthority对象存入authorities中
        authorities = permissions.stream().
                map(SimpleGrantedAuthority::new)
                .collect(Collectors.toList());
        return authorities;
    }

    @Override
    public String getPassword() {
        return user.getPassword();
    }

    @Override
    public String getUsername() {
        return user.getUserName();
    }

    @Override
    public boolean isAccountNonExpired() {
        return true;
    }

    @Override
    public boolean isAccountNonLocked() {
        return true;
    }

    @Override
    public boolean isCredentialsNonExpired() {
        return true;
    }

    @Override
    public boolean isEnabled() {
        return true;
    }
}
```
![image.png](https://cdn.nlark.com/yuque/0/2022/png/22223333/1660653225953-210fd150-f420-49f1-99b1-420d9e7f4267.png#clientId=u376b5a7a-5be9-4&crop=0&crop=0&crop=1&crop=1&from=paste&height=538&id=ue2a1e50a&margin=%5Bobject%20Object%5D&name=image.png&originHeight=807&originWidth=1208&originalType=binary&ratio=1&rotation=0&showTitle=false&size=131744&status=done&style=none&taskId=ub174f6ac-790c-4d79-a551-1e76c7b6345&title=&width=805.3333333333334)

2. LoginUser修改完后我们就可以在UserDetailsServiceImpl中去把权限信息封装到LoginUser中了。我们写死权限进行测试，后面我们再从数据库中查询权限信息。
```java
@Service
public class UserDetailsServiceImpl implements UserDetailsService {

    @Autowired
    private UserMapper userMapper;

    @Override
    public UserDetails loadUserByUsername(String username) throws UsernameNotFoundException {
        //根据用户名查询用户信息
        LambdaQueryWrapper<User> wrapper = new LambdaQueryWrapper<>();
        wrapper.eq(User::getUserName, username);
        User user = userMapper.selectOne(wrapper);
        //如果查询不到数据就通过抛出异常来给出提示
        if (Objects.isNull(user)) {
            throw new RuntimeException("用户名或密码错误");
        }

        //TODO 根据用户查询权限信息 添加到LoginUser中
        List<String> list = new ArrayList<>(Arrays.asList("test"));

        //封装成UserDetails对象返回
        return new LoginUser(user, list);
    }
}
```
![image.png](https://cdn.nlark.com/yuque/0/2022/png/22223333/1660653393521-b59c4be4-2086-4694-9ada-4e6cb5c2608d.png#clientId=u376b5a7a-5be9-4&crop=0&crop=0&crop=1&crop=1&from=paste&height=133&id=u10b74e00&margin=%5Bobject%20Object%5D&name=image.png&originHeight=200&originWidth=908&originalType=binary&ratio=1&rotation=0&showTitle=false&size=34712&status=done&style=none&taskId=u4675aa71-e1c5-4983-9ccd-430b9fb11f9&title=&width=605.3333333333334)

3. 在JwtAuthenticationTokenFilter把LoginUser的权限信息封装给SecurityContextHolder

![image.png](https://cdn.nlark.com/yuque/0/2022/png/22223333/1660658082269-09411fda-30c3-4424-ad93-fd72c6334fb4.png#clientId=u376b5a7a-5be9-4&crop=0&crop=0&crop=1&crop=1&from=paste&height=153&id=u3a00749d&margin=%5Bobject%20Object%5D&name=image.png&originHeight=229&originWidth=1451&originalType=binary&ratio=1&rotation=0&showTitle=false&size=65659&status=done&style=none&taskId=u7a80174e-524a-46ae-b3f1-2c05b3c985d&title=&width=967.3333333333334)

4. 测试一下

添加@PreAuthorize注解，需要让访问的用户拥有指定的权限才能访问
```java
@RestController
@RequestMapping("/hello")
public class TestController {

    @GetMapping("{name}")
    @PreAuthorize("hasAuthority('test')")
    public String getHello(@PathVariable String name) {
        return "Hello " + name;
    }
}
```
上面已经封装了固定的test权限，让封装的用户进行访问
![image.png](https://cdn.nlark.com/yuque/0/2022/png/22223333/1660657311333-3d74220a-356d-4a20-86bf-e3ddb799dc55.png#clientId=u376b5a7a-5be9-4&crop=0&crop=0&crop=1&crop=1&from=paste&height=339&id=uc7cb4c07&margin=%5Bobject%20Object%5D&name=image.png&originHeight=508&originWidth=1423&originalType=binary&ratio=1&rotation=0&showTitle=false&size=64519&status=done&style=none&taskId=u1d30157f-14e8-4ebb-86fe-4e36f831029&title=&width=948.6666666666666)
更改访问权限后再次访问
![image.png](https://cdn.nlark.com/yuque/0/2022/png/22223333/1660657365795-bedc0bdd-3b7b-43c4-8851-116ccb62fcf9.png#clientId=u376b5a7a-5be9-4&crop=0&crop=0&crop=1&crop=1&from=paste&height=229&id=u0bd5ab28&margin=%5Bobject%20Object%5D&name=image.png&originHeight=343&originWidth=960&originalType=binary&ratio=1&rotation=0&showTitle=false&size=46521&status=done&style=none&taskId=uc6246df8-2e69-4d4f-97d4-2222925dd52&title=&width=640)
![image.png](https://cdn.nlark.com/yuque/0/2022/png/22223333/1660657402951-03d59d00-3ff3-446d-9e95-767aed78f138.png#clientId=u376b5a7a-5be9-4&crop=0&crop=0&crop=1&crop=1&from=paste&height=373&id=u3bccf39e&margin=%5Bobject%20Object%5D&name=image.png&originHeight=560&originWidth=1417&originalType=binary&ratio=1&rotation=0&showTitle=false&size=70547&status=done&style=none&taskId=ua8f8d4a2-6a18-49ff-ba77-7b2cdbbc60b&title=&width=944.6666666666666)

5. 也可以在Security配置类中配置访问权限

![image.png](https://cdn.nlark.com/yuque/0/2022/png/22223333/1660657739154-bc90d9fb-43be-4425-941a-577470ddf820.png#clientId=u376b5a7a-5be9-4&crop=0&crop=0&crop=1&crop=1&from=paste&height=133&id=uccb2585d&margin=%5Bobject%20Object%5D&name=image.png&originHeight=199&originWidth=1003&originalType=binary&ratio=1&rotation=0&showTitle=false&size=38226&status=done&style=none&taskId=u574d0790-13b7-44a9-8d5c-ac8de548901&title=&width=668.6666666666666)
anonymous() 允许匿名用户访问,不允许已登入用户访问
permitAll() 不管登入,不登入 都能访问
### 3.3 封装从数据库中查询的权限信息
建立的数据库模型如下，采用RBAC权限模型（参考3.1），多对多的关系需要使用另外的一张表进行连接，变为一对多的关系
![image.png](https://cdn.nlark.com/yuque/0/2022/png/22223333/1660663485295-c9e9db03-113d-4367-afb3-b0e53fa5ba35.png#clientId=u376b5a7a-5be9-4&crop=0&crop=0&crop=1&crop=1&from=paste&height=589&id=u03f8cc8f&margin=%5Bobject%20Object%5D&name=image.png&originHeight=883&originWidth=1110&originalType=binary&ratio=1&rotation=0&showTitle=false&size=465851&status=done&style=none&taskId=ub4963a93-03a5-4f59-9fe2-3980f7b1410&title=&width=740)
#### 3.3.1 MySql数据表
```sql
SET NAMES utf8mb4;
SET FOREIGN_KEY_CHECKS = 0;

-- ----------------------------
-- Table structure for sys_menu
-- ----------------------------
DROP TABLE IF EXISTS `sys_menu`;
CREATE TABLE `sys_menu`  (
  `id` bigint(0) NOT NULL AUTO_INCREMENT,
  `menu_name` varchar(64) CHARACTER SET utf8mb4 COLLATE utf8mb4_0900_ai_ci NOT NULL DEFAULT 'NULL' COMMENT '菜单名',
  `path` varchar(200) CHARACTER SET utf8mb4 COLLATE utf8mb4_0900_ai_ci NULL DEFAULT NULL COMMENT '路由地址',
  `component` varchar(255) CHARACTER SET utf8mb4 COLLATE utf8mb4_0900_ai_ci NULL DEFAULT NULL COMMENT '组件路径',
  `visible` char(1) CHARACTER SET utf8mb4 COLLATE utf8mb4_0900_ai_ci NULL DEFAULT '0' COMMENT '菜单状态（0显示 1隐藏）',
  `status` char(1) CHARACTER SET utf8mb4 COLLATE utf8mb4_0900_ai_ci NULL DEFAULT '0' COMMENT '菜单状态（0正常 1停用）',
  `perms` varchar(100) CHARACTER SET utf8mb4 COLLATE utf8mb4_0900_ai_ci NULL DEFAULT NULL COMMENT '权限标识',
  `icon` varchar(100) CHARACTER SET utf8mb4 COLLATE utf8mb4_0900_ai_ci NULL DEFAULT '#' COMMENT '菜单图标',
  `create_by` bigint(0) NULL DEFAULT NULL,
  `create_time` datetime(0) NULL DEFAULT NULL,
  `update_by` bigint(0) NULL DEFAULT NULL,
  `update_time` datetime(0) NULL DEFAULT NULL,
  `del_flag` int(0) NULL DEFAULT 0 COMMENT '是否删除（0未删除 1已删除）',
  `remark` varchar(500) CHARACTER SET utf8mb4 COLLATE utf8mb4_0900_ai_ci NULL DEFAULT NULL COMMENT '备注',
  PRIMARY KEY (`id`) USING BTREE
) ENGINE = InnoDB AUTO_INCREMENT = 2 CHARACTER SET = utf8mb4 COLLATE = utf8mb4_0900_ai_ci COMMENT = '菜单表' ROW_FORMAT = Dynamic;

-- ----------------------------
-- Records of sys_menu
-- ----------------------------
INSERT INTO `sys_menu` VALUES (2, '查询图书列表', 'system/book/list', 'book', '0', '0', 'system:book:list', '#', NULL, NULL, NULL, NULL, 0, NULL);
INSERT INTO `sys_menu` VALUES (3, '添加图书', 'system/book/add', 'book', '0', '0', 'system:book:add', '#', NULL, NULL, NULL, NULL, 0, NULL);
INSERT INTO `sys_menu` VALUES (4, '删除图书', 'system/book/delete', 'book', '0', '0', 'system:book:delete', '#', NULL, NULL, NULL, NULL, 0, NULL);
INSERT INTO `sys_menu` VALUES (5, '借阅图书', 'system/book/borrow', 'book', '0', '0', 'system:book:borrow', '#', NULL, NULL, NULL, NULL, 0, NULL);
INSERT INTO `sys_menu` VALUES (6, '修改用户信息', 'system/user/update', 'user', '0', '0', 'system:user:update', '#', NULL, NULL, NULL, NULL, 0, NULL);
INSERT INTO `sys_menu` VALUES (7, '添加用户信息', 'system/user/add', 'user', '0', '0', 'system:user:add', '#', NULL, NULL, NULL, NULL, 0, NULL);
INSERT INTO `sys_menu` VALUES (8, '删除用户信息', 'system/user/delete', 'user', '0', '0', 'system:user:delete', '#', NULL, NULL, NULL, NULL, 0, NULL);
INSERT INTO `sys_menu` VALUES (9, '查询用户信息', 'system/user/query', 'user', '0', '0', 'system:user:query', '#', NULL, NULL, NULL, NULL, 0, NULL);

-- ----------------------------
-- Table structure for sys_role
-- ----------------------------
DROP TABLE IF EXISTS `sys_role`;
CREATE TABLE `sys_role`  (
  `id` bigint(0) NOT NULL AUTO_INCREMENT,
  `name` varchar(128) CHARACTER SET utf8mb4 COLLATE utf8mb4_0900_ai_ci NULL DEFAULT NULL,
  `role_key` varchar(100) CHARACTER SET utf8mb4 COLLATE utf8mb4_0900_ai_ci NULL DEFAULT NULL COMMENT '角色权限字符串',
  `status` char(1) CHARACTER SET utf8mb4 COLLATE utf8mb4_0900_ai_ci NULL DEFAULT '0' COMMENT '角色状态（0正常 1停用）',
  `del_flag` int(0) NULL DEFAULT 0 COMMENT 'del_flag',
  `create_by` bigint(0) NULL DEFAULT NULL,
  `create_time` datetime(0) NULL DEFAULT NULL,
  `update_by` bigint(0) NULL DEFAULT NULL,
  `update_time` datetime(0) NULL DEFAULT NULL,
  `remark` varchar(500) CHARACTER SET utf8mb4 COLLATE utf8mb4_0900_ai_ci NULL DEFAULT NULL COMMENT '备注',
  PRIMARY KEY (`id`) USING BTREE
) ENGINE = InnoDB AUTO_INCREMENT = 3 CHARACTER SET = utf8mb4 COLLATE = utf8mb4_0900_ai_ci COMMENT = '角色表' ROW_FORMAT = Dynamic;

-- ----------------------------
-- Records of sys_role
-- ----------------------------
INSERT INTO `sys_role` VALUES (3, '普通用户', 'ROLE_consumer', '0', 0, NULL, NULL, NULL, NULL, NULL);
INSERT INTO `sys_role` VALUES (4, '图书管理员', 'ROLE_librarian', '0', 0, NULL, NULL, NULL, NULL, NULL);

-- ----------------------------
-- Table structure for sys_role_menu
-- ----------------------------
DROP TABLE IF EXISTS `sys_role_menu`;
CREATE TABLE `sys_role_menu`  (
  `role_id` bigint(0) NOT NULL AUTO_INCREMENT COMMENT '角色ID',
  `menu_id` bigint(0) NOT NULL DEFAULT 0 COMMENT '菜单id',
  PRIMARY KEY (`role_id`, `menu_id`) USING BTREE
) ENGINE = InnoDB AUTO_INCREMENT = 2 CHARACTER SET = utf8mb4 COLLATE = utf8mb4_0900_ai_ci ROW_FORMAT = Dynamic;

-- ----------------------------
-- Records of sys_role_menu
-- ----------------------------
INSERT INTO `sys_role_menu` VALUES (3, 2);
INSERT INTO `sys_role_menu` VALUES (3, 5);
INSERT INTO `sys_role_menu` VALUES (3, 9);
INSERT INTO `sys_role_menu` VALUES (4, 2);
INSERT INTO `sys_role_menu` VALUES (4, 3);
INSERT INTO `sys_role_menu` VALUES (4, 4);
INSERT INTO `sys_role_menu` VALUES (4, 5);
INSERT INTO `sys_role_menu` VALUES (4, 6);
INSERT INTO `sys_role_menu` VALUES (4, 7);
INSERT INTO `sys_role_menu` VALUES (4, 8);
INSERT INTO `sys_role_menu` VALUES (4, 9);

-- ----------------------------
-- Table structure for sys_user_role
-- ----------------------------
DROP TABLE IF EXISTS `sys_user_role`;
CREATE TABLE `sys_user_role`  (
  `user_id` bigint(0) NOT NULL AUTO_INCREMENT COMMENT '用户id',
  `role_id` bigint(0) NOT NULL DEFAULT 0 COMMENT '角色id',
  PRIMARY KEY (`user_id`, `role_id`) USING BTREE
) ENGINE = InnoDB CHARACTER SET = utf8mb4 COLLATE = utf8mb4_0900_ai_ci ROW_FORMAT = Dynamic;

-- ----------------------------
-- Records of sys_user_role
-- ----------------------------
INSERT INTO `sys_user_role` VALUES (3, 4);
INSERT INTO `sys_user_role` VALUES (4, 3);

SET FOREIGN_KEY_CHECKS = 1;
```
通过sys_user的id查询用户所拥有的权限
```sql
SELECT 
	DISTINCT m.`perms` as `权限`, r.`name` as `角色`
FROM
	sys_user_role ur
	LEFT JOIN `sys_role` r ON ur.`role_id` = r.`id`
	LEFT JOIN `sys_role_menu` rm ON ur.`role_id` = rm.`role_id`
	LEFT JOIN `sys_menu` m ON m.`id` = rm.`menu_id`
WHERE
	user_id = 3
```
#### 3.3.2 代码实现 
添加权限单实体类
```java
@TableName(value = "sys_menu")
@Data
@AllArgsConstructor
@NoArgsConstructor
@JsonInclude(JsonInclude.Include.NON_NULL)
public class Menu implements Serializable {
    private static final long serialVersionUID = -54979041104113736L;

    @TableId
    private Long id;
    /**
     * 菜单名
     */
    private String menuName;
    /**
     * 路由地址
     */
    private String path;
    /**
     * 组件路径
     */
    private String component;
    /**
     * 菜单状态（0显示 1隐藏）
     */
    private String visible;
    /**
     * 菜单状态（0正常 1停用）
     */
    private String status;
    /**
     * 权限标识
     */
    private String perms;
    /**
     * 菜单图标
     */
    private String icon;

    private Long createBy;

    private Date createTime;

    private Long updateBy;

    private Date updateTime;
    /**
     * 是否删除（0未删除 1已删除）
     */
    private Integer delFlag;
    /**
     * 备注
     */
    private String remark;
}
```
添加通过sys_user的id查询用户所拥有的权限的方法
```java
@Mapper
@Repository
public interface MenuMapper extends BaseMapper<Menu> {
    List<String> selectPermsByUserId(Long id);
}
```
```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN" "http://mybatis.org/dtd/mybatis-3-mapper.dtd" >
<mapper namespace="com.securityJWT.mapper.MenuMapper">
    <select id="selectPermsByUserId" resultType="java.lang.String">
        SELECT
	        DISTINCT m.`perms`
        FROM
            sys_user_role ur
        LEFT JOIN `sys_role` r ON ur.`role_id` = r.`id`
        LEFT JOIN `sys_role_menu` rm ON ur.`role_id` = rm.`role_id`
        LEFT JOIN `sys_menu` m ON m.`id` = rm.`menu_id`
        WHERE
	        user_id = #{id}
    </select>
</mapper>
```
更改UserDetailsService实现类的查询，将用户权限封装进LoginUser
```java
    @Autowired
    private MenuMapper menuMapper;

List<String> perms = menuMapper.selectPermsByUserId(user.getId());
```
![image.png](https://cdn.nlark.com/yuque/0/2022/png/22223333/1660661488203-60d12728-54fd-458f-a68b-481d8c5ffac9.png#clientId=u376b5a7a-5be9-4&crop=0&crop=0&crop=1&crop=1&from=paste&height=196&id=ufab11f1b&margin=%5Bobject%20Object%5D&name=image.png&originHeight=294&originWidth=1074&originalType=binary&ratio=1&rotation=0&showTitle=false&size=32906&status=done&style=none&taskId=u894fd238-95c0-465b-ae19-e5ffc551a39&title=&width=716)
![image.png](https://cdn.nlark.com/yuque/0/2022/png/22223333/1660661525263-6d1f6c2a-8531-406a-bbb5-f5ca2f6b289c.png#clientId=u376b5a7a-5be9-4&crop=0&crop=0&crop=1&crop=1&from=paste&height=428&id=ub5faadc7&margin=%5Bobject%20Object%5D&name=image.png&originHeight=642&originWidth=1334&originalType=binary&ratio=1&rotation=0&showTitle=false&size=128546&status=done&style=none&taskId=uba93c53f-034a-4b63-9358-efe5d01f090&title=&width=889.3333333333334)
#### 3.3.3 权限测试
当前接口所需要的权限
![image.png](https://cdn.nlark.com/yuque/0/2022/png/22223333/1660662846792-2e66284f-5c05-47ea-a78d-8bf9ae086343.png#clientId=u376b5a7a-5be9-4&crop=0&crop=0&crop=1&crop=1&from=paste&height=194&id=u03114852&margin=%5Bobject%20Object%5D&name=image.png&originHeight=291&originWidth=1303&originalType=binary&ratio=1&rotation=0&showTitle=false&size=46522&status=done&style=none&taskId=u95fcfb01-c425-44fa-a17d-6cc263ef088&title=&width=868.6666666666666)
当前用户所拥有的权限
![image.png](https://cdn.nlark.com/yuque/0/2022/png/22223333/1660662966044-0637d1a0-757d-4357-802a-c7cfd69697ee.png#clientId=u376b5a7a-5be9-4&crop=0&crop=0&crop=1&crop=1&from=paste&height=283&id=ua4748f1e&margin=%5Bobject%20Object%5D&name=image.png&originHeight=425&originWidth=1093&originalType=binary&ratio=1&rotation=0&showTitle=false&size=54147&status=done&style=none&taskId=uccd7e6bd-98f2-427d-b473-bed23b5601d&title=&width=728.6666666666666)
对该接口进行访问，可以访问成功
![image.png](https://cdn.nlark.com/yuque/0/2022/png/22223333/1660663041953-07e16fee-57a5-47dc-86fe-d6acd0f921a5.png#clientId=u376b5a7a-5be9-4&crop=0&crop=0&crop=1&crop=1&from=paste&height=364&id=ue3e094e2&margin=%5Bobject%20Object%5D&name=image.png&originHeight=546&originWidth=1408&originalType=binary&ratio=1&rotation=0&showTitle=false&size=73397&status=done&style=none&taskId=u2f16055a-9cb2-4f3e-b85b-e2f8d359d0d&title=&width=938.6666666666666)
### 3.4 自定义失败处理
如果是认证过程中出现的异常会被封装成AuthenticationException然后调用AuthenticationEntryPoint对象的方法去进行异常处理。
如果是授权过程中出现的异常会被封装成AccessDeniedException然后调用AccessDeniedHandler对象的方法去进行异常处理。
所以如果我们需要自定义异常处理，我们只需要自定义AuthenticationEntryPoint和AccessDeniedHandler然后配置给SpringSecurity即可。
#### 3.4.1 自定义实现类
```java
@Component
public class AccessDeniedHandlerImpl implements AccessDeniedHandler {
    @Override
    public void handle(HttpServletRequest request, HttpServletResponse response, AccessDeniedException e) throws IOException, ServletException {
        ResponseUtil.sendMsg(response, HttpStatus.FORBIDDEN.value(), "权限不足");
    }
}
```
```java
@Component
public class AuthenticationEntryPointImpl implements AuthenticationEntryPoint {
    @Override
    public void commence(HttpServletRequest request, HttpServletResponse response, AuthenticationException e) throws IOException, ServletException {
        ResponseUtil.sendMsg(response, HttpStatus.UNAUTHORIZED.value(), "认证失败请重新登录");
    }
}
```
```java
public class ResponseUtil {

    public static void tokenExpire(HttpServletResponse response) throws IOException {
        response.setHeader("Access-Control-Allow-Origin", "*");
        response.setHeader("Cache-Control", "no-cache");
        response.setCharacterEncoding("UTF-8");
        response.setContentType("application/json");
        HashMap<String, Object> map = new HashMap<>();
        map.put("code", 401);
        map.put("data", "Full authentication is required to access this resource");
        map.put("message", "token已过期");
        ServletOutputStream output = response.getOutputStream();
        output.write(JSON.toJSONBytes(map));
        output.flush();
    }

    public static void sendMsg(HttpServletResponse response, int httpStatus, String msg) throws IOException {
        response.setHeader("Access-Control-Allow-Origin", "*");
        response.setHeader("Cache-Control", "no-cache");
        response.setCharacterEncoding("UTF-8");
        response.setContentType("application/json");
        HashMap<String, Object> map = new HashMap<>();
        map.put("code", httpStatus);
        map.put("message", msg);
        ServletOutputStream output = response.getOutputStream();
        output.write(JSON.toJSONBytes(map));
        output.flush();
    }
}
```
#### 3.4.2 配置给SpringSecurity
 先注入对应的处理器
```java
    @Autowired
    private AccessDeniedHandlerImpl accessDeniedHandler;

    @Autowired
    private AuthenticationEntryPointImpl authenticationEntryPoint;
```
使用HttpSecurity对象的方法去配置
```java
// 自定义失败处理
http.exceptionHandling().authenticationEntryPoint(authenticationEntryPoint)
    .accessDeniedHandler(accessDeniedHandler);
```
![image.png](https://cdn.nlark.com/yuque/0/2022/png/22223333/1660664101051-bc888639-e2ee-45fd-b7ac-40968c2892a9.png#clientId=u376b5a7a-5be9-4&crop=0&crop=0&crop=1&crop=1&from=paste&height=271&id=u07ab1b8e&margin=%5Bobject%20Object%5D&name=image.png&originHeight=406&originWidth=1376&originalType=binary&ratio=1&rotation=0&showTitle=false&size=66407&status=done&style=none&taskId=u5961630a-8e5c-4e05-adba-48b4aecb7d9&title=&width=917.3333333333334)![image.png](https://cdn.nlark.com/yuque/0/2022/png/22223333/1660664123782-916be2a9-fdc6-4179-ab3e-e990c73ebb1a.png#clientId=u376b5a7a-5be9-4&crop=0&crop=0&crop=1&crop=1&from=paste&height=158&id=uf2f58cff&margin=%5Bobject%20Object%5D&name=image.png&originHeight=237&originWidth=1355&originalType=binary&ratio=1&rotation=0&showTitle=false&size=43462&status=done&style=none&taskId=u75949175-7569-468b-b0ca-45c45502ff3&title=&width=903.3333333333334)
### 3.5 其他处理器
#### 3.5.1 认证成功和认证失败处理器
AuthenticationSuccessHandler
AuthenticationFailureHandler
实现并重写上面的接口，之后在Security配置类中配置
```java
http.formLogin()
    // 配置认证成功处理器
    .successHandler(successHandler)
    // 配置认证失败处理器
    .failureHandler(failureHandler);
```
#### 3.5.2 注销成功处理器
LogoutSuccessHandler
实现并重写上面的接口，之后在Security配置类中配置
```java
http.logout()
    //配置注销成功处理器
    .logoutSuccessHandler(logoutSuccessHandler);
```
