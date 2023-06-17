---
title: "SpringBoot AOP"
description: "SpringBoot AOP"
keywords: "AOP"

date: 2023-06-16T16:41:30+08:00
lastmod: 2023-06-16T16:41:30+08:00

categories:
  - 框架和中间件
tags:
  - AOP
---

# 1. 前言
aop面向切面编程，是编程中一个很重要的思想本篇文章主要介绍的是SpringBoot切面Aop的使用和案例
# 2. 什么是aop
AOP（Aspect OrientedProgramming）：面向切面编程，面向切面编程（也叫面向方面编程），是目前软件开发中的一个热点，也是Spring框架中的一个重要内容。利用AOP可以对业务逻辑的各个部分进行隔离，从而使得业务逻辑各部分之间的耦合度降低，提高程序的可重用性，同时提高了开发的效率。
# 3. 使用场景
利用AOP可以对我们边缘业务进行隔离，降低无关业务逻辑耦合性。提高程序的可重用性，同时提高了开发的效率。一般用于日志记录，性能统计，安全控制，权限管理，事务处理，异常处理，资源池管理。使用场景
# 4. 为什么需要面向切面编程
面向对象编程（OOP）的好处是显而易见的，缺点也同样明显。当需要为多个不具有继承关系的对象添加一个公共的方法的时候，例如日志记录、性能监控等，如果采用面向对象编程的方法，需要在每个对象里面都添加相同的方法，这样就产生了较大的重复工作量和大量的重复代码，不利于维护。面向切面编程（AOP）是面向对象编程的补充，简单来说就是统一处理某一“切面”的问题的编程思想。如果使用AOP的方式进行日志的记录和处理，所有的日志代码都集中于一处，不需要再每个方法里面都去添加，极大减少了重复代码。
# 5. 技术要点

1. 通知（Advice）包含了需要用于多个应用对象的横切行为，完全听不懂，没关系，通俗一点说就是定义了“什么时候”和“做什么”。
2. 连接点（Join Point）是程序执行过程中能够应用通知的所有点。
3. 切点（Poincut）是定义了在“什么地方”进行切入，哪些连接点会得到通知。显然，切点一定是连接点。
4. 切面（Aspect）是通知和切点的结合。通知和切点共同定义了切面的全部内容——是什么，何时，何地完成功能。
5. 引入（Introduction）允许我们向现有的类中添加新方法或者属性。
6. 织入（Weaving）是把切面应用到目标对象并创建新的代理对象的过程，分为编译期织入、类加载期织入和运行期织入。
# 6. 整合使用
## 6.1 导入依赖
在springboot中使用aop要导aop依赖
```xml
 <!--aop 切面-->
<dependency>
  <groupId>org.springframework.boot</groupId>
  <artifactId>spring-boot-starter-aop</artifactId>
</dependency>
```
注意这里版本依赖于spring-boot-start-parent父pom中的spring-boot-dependencies
## 6.2 编写拦截的bean
这里我们定义一个controller用于拦截所有请求的记录
```java
@RestController
public class AopController {

    @RequestMapping("/hello")
    public String sayHello(){
        System.out.println("hello");
        return "hello";
    }
}
```
## 6.3 定义切面
SpringBoot在使用切面的时候采用@Aspect注解对POJO进行标注，该注解表明该类不仅仅是一个POJO，还是一个切面容器
## 6.4 定义切点
切点是通过@Pointcut注解和切点表达式定义的。
@Pointcut注解可以在一个切面内定义可重用的切点。
由于Spring切面粒度最小是达到方法级别，而execution表达式可以用于明确指定方法返回类型，类名，方法名和参数名等与方法相关的部件，并且实际中，大部分需要使用AOP的业务场景也只需要达到方法级别即可，因而execution表达式的使用是最为广泛的。如图是execution表达式的语法：
![image.png](https://cdn.nlark.com/yuque/0/2022/png/22223333/1660547032639-fafa6d1d-2be9-4465-b39f-5937cb420e76.png#clientId=u261875d5-9e35-4&crop=0&crop=0&crop=1&crop=1&from=paste&id=uc358267e&margin=%5Bobject%20Object%5D&name=image.png&originHeight=244&originWidth=1240&originalType=url&ratio=1&rotation=0&showTitle=false&size=96724&status=done&style=none&taskId=u8f882bbc-4940-4c7f-b6f1-a496a90bb62&title=)
execution表示在方法执行的时候触发。以“”开头，表明方法返回值类型为任意类型。然后是全限定的类名和方法名，“”可以表示任意类和任意方法。对于方法参数列表，可以使用“..”表示参数为任意类型。如果需要多个表达式，可以使用“&&”、“||”和“!”完成与、或、非的操作。
## 6.5 定义通知
通知有五种类型，分别是：

1. 前置通知（@Before）：在目标方法调用之前调用通知
2. 后置通知（@After）：在目标方法完成之后调用通知
3. 环绕通知（@Around）：在被通知的方法调用之前和调用之后执行自定义的方法
4. 返回通知（@AfterReturning）：在目标方法成功执行之后调用通知
5. 异常通知（@AfterThrowing）：在目标方法抛出异常之后调用通知

代码中定义了三种类型的通知，使用@Before注解标识前置通知，打印“beforeAdvice...”，使用@After注解标识后置通知，打印“AfterAdvice...”，使用@Around注解标识环绕通知，在方法执行前和执行之后分别打印“before”和“after”。这样一个切面就定义好了，代码如下：
> **案例一**

```java
@Aspect
@Component
public class AopAdvice {

    @Pointcut("execution (* com.shangguan.aop.controller.*.*(..))")
    public void test() {

    }

    @Before("test()")
    public void beforeAdvice() {
        System.out.println("beforeAdvice...");
    }

    @After("test()")
    public void afterAdvice() {
        System.out.println("afterAdvice...");
    }

    @Around("test()")
    public void aroundAdvice(ProceedingJoinPoint proceedingJoinPoint) {
        System.out.println("before");
        try {
            proceedingJoinPoint.proceed();
        } catch (Throwable t) {
            t.printStackTrace();
        }
        System.out.println("after");
    }

}
```
![image.png](https://cdn.nlark.com/yuque/0/2022/png/22223333/1660547083776-f887eefb-3ae4-47b8-85d8-33f46c0171be.png#clientId=u261875d5-9e35-4&crop=0&crop=0&crop=1&crop=1&from=paste&id=uf9ec091c&margin=%5Bobject%20Object%5D&name=image.png&originHeight=423&originWidth=1240&originalType=url&ratio=1&rotation=0&showTitle=false&size=132465&status=done&style=none&taskId=u063352a1-393b-4866-ba65-43510d5412c&title=)
> **案例二**

- 定义接口
```java
public interface IJdkProxyService {

    void doMethod1();

    String doMethod2();

    String doMethod3() throws Exception;
}
```

- 实现类
```java
@Service
public class JdkProxyDemoServiceImpl implements IJdkProxyService {

    @Override
    public void doMethod1() {
        System.out.println("JdkProxyServiceImpl.doMethod1()");
    }

    @Override
    public String doMethod2() {
        System.out.println("JdkProxyServiceImpl.doMethod2()");
        return "hello world";
    }

    @Override
    public String doMethod3() throws Exception {
        System.out.println("JdkProxyServiceImpl.doMethod3()");
        throw new Exception("some exception");
    }
}
```

- **定义切面**
```java
@EnableAspectJAutoProxy
@Component
@Aspect
public class LogAspect {

    /**
     * define point cut.
     */
    @Pointcut("execution(* tech.pdai.springframework.service.*.*(..))")
    private void pointCutMethod() {
    }


    /**
     * 环绕通知.
     *
     * @param pjp pjp
     * @return obj
     * @throws Throwable exception
     */
    @Around("pointCutMethod()")
    public Object doAround(ProceedingJoinPoint pjp) throws Throwable {
        System.out.println("-----------------------");
        System.out.println("环绕通知: 进入方法");
        Object o = pjp.proceed();
        System.out.println("环绕通知: 退出方法");
        return o;
    }

    /**
     * 前置通知.
     */
    @Before("pointCutMethod()")
    public void doBefore() {
        System.out.println("前置通知");
    }


    /**
     * 后置通知.
     *
     * @param result return val
     */
    @AfterReturning(pointcut = "pointCutMethod()", returning = "result")
    public void doAfterReturning(String result) {
        System.out.println("后置通知, 返回值: " + result);
    }

    /**
     * 异常通知.
     *
     * @param e exception
     */
    @AfterThrowing(pointcut = "pointCutMethod()", throwing = "e")
    public void doAfterThrowing(Exception e) {
        System.out.println("异常通知, 异常: " + e.getMessage());
    }

    /**
     * 最终通知.
     */
    @After("pointCutMethod()")
    public void doAfter() {
        System.out.println("最终通知");
    }

}
```

- **输出**
```bash
-----------------------
环绕通知: 进入方法
前置通知
JdkProxyServiceImpl.doMethod1()
最终通知
环绕通知: 退出方法
-----------------------
环绕通知: 进入方法
前置通知
JdkProxyServiceImpl.doMethod2()
后置通知, 返回值: hello world
最终通知
环绕通知: 退出方法
-----------------------
环绕通知: 进入方法
前置通知
JdkProxyServiceImpl.doMethod3()
异常通知, 异常: some exception
最终通知
```
