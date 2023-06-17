---
title: "RabbitMQ入门"
description: "RabbirMQ入门"
keywords: "RabbitMQ"

date: 2023-06-16T16:41:30+08:00
lastmod: 2023-06-16T16:41:30+08:00

categories:
  - 框架和中间件
tags:
  - RabbitMQ
---

## 1. 安装
### 1.1 docker安装
```bash
docker pull rabbitmq:management 
```
> **默认用户名和密码：guest/guest**

```bash
docker run -dit \
--name rabbitmq \
-p 5672:5672 \
-p 15672:15672 \
rabbitmq:management
```
| 端口 | 作用 |
| --- | --- |
| 15672 | 管理界面UI的使用端口 |
| 15671 | 管理监听端口 |
| 5672,5671 | AMQP 0-9-1 without and with TLSclient端通信口 |
| 4369 | (epmd)epmd代表Erlang端口映射守护进程，erlang发现口 |
| 25672 | ( Erlang distribution) server间内部通信口 |


## 2. 核心内容
### 2.1 常用队列
![image.png](https://cdn.nlark.com/yuque/0/2022/png/22223333/1656726572218-ce8939cd-8766-42b5-a0f6-0636defd30d3.png#clientId=u0fc8191a-cfe6-4&crop=0&crop=0&crop=1&crop=1&from=paste&height=229&id=u7a864471&margin=%5Bobject%20Object%5D&name=image.png&originHeight=229&originWidth=893&originalType=binary&ratio=1&rotation=0&showTitle=false&size=35434&status=done&style=none&taskId=u64fc3c9f-2695-4e82-9d8e-ba553968ff5&title=&width=893)![image.png](https://cdn.nlark.com/yuque/0/2022/png/22223333/1656726586768-c8956072-d26a-484f-8990-b4b0387d390d.png#clientId=u0fc8191a-cfe6-4&crop=0&crop=0&crop=1&crop=1&from=paste&height=207&id=u7b0e5747&margin=%5Bobject%20Object%5D&name=image.png&originHeight=207&originWidth=967&originalType=binary&ratio=1&rotation=0&showTitle=false&size=35058&status=done&style=none&taskId=uf7a97366-ce16-4db2-b2a5-9f181d7b144&title=&width=967)
### 2.2 工作原理
![image.png](https://cdn.nlark.com/yuque/0/2022/png/22223333/1656726858021-d9d19355-4174-4869-81c4-af4409443e1e.png#clientId=u0fc8191a-cfe6-4&crop=0&crop=0&crop=1&crop=1&from=paste&height=374&id=u99ff43b0&margin=%5Bobject%20Object%5D&name=image.png&originHeight=374&originWidth=743&originalType=binary&ratio=1&rotation=0&showTitle=false&size=100631&status=done&style=none&taskId=u04ac6c02-f01f-4068-8668-46a0268c338&title=&width=743)
### 2.3 名词解释
**Broker**：接收和分发消息的应用，RabbitMQ Server 就是 Message Broker
**Virtual host**：出于多租户和安全因素设计的，把 AMQP 的基本组件划分到一个虚拟的分组中，类似于网络中的 namespace 概念。当多个不同的用户使用同一个 RabbitMQ server 提供的服务时，可以划分出多个 vhost，每个用户在自己的 vhost 创建 exchange／queue 等
**Connection**：publisher／consumer 和 broker 之间的 TCP 连接
**Channel**：如果每一次访问 RabbitMQ 都建立一个 Connection，在消息量大的时候建立 TCP
Connection 的开销将是巨大的，效率也较低。Channel 是在 connection 内部建立的逻辑连接，如果应用程序支持多线程，通常每个 thread 创建单独的 channel 进行通讯，AMQP method 包含了 channel id 帮助客户端和 message broker 识别 channel，所以 channel 之间是完全隔离的。**Channel 作为轻量级的Connection 极大减少了操作系统建立 TCP connection 的开销**
**Exchange**：message 到达 broker 的第一站，根据分发规则，匹配查询表中的 routing key，分发消息到 queue 中去。常用的类型有：direct (point-to-point), topic (publish-subscribe) and fanout(multicast)
**Queue**：消息最终被送到这里等待 consumer 取走
**Binding**：exchange 和 queue 之间的虚拟连接，binding 中可以包含 routing key，Binding 信息被保存到 exchange 中的查询表中，用于 message 的分发依据
## 3. 快速开始
### 3.1 导入依赖
```java
<dependency>
    <groupId>com.rabbitmq</groupId>
    <artifactId>amqp-client</artifactId>
    <version>5.15.0</version>
</dependency>
```
### 3.2 封装获取连接方式
```java
public class RabbitMqUtils {
    public static Connection getConnection() throws Exception {
        // 获取连接工厂
        ConnectionFactory factory = new ConnectionFactory();
        factory.setHost("localhost");
        factory.setPort(5672);
        factory.setUsername("guest");
        factory.setPassword("guest");
        Connection connection = factory.newConnection();
        return connection;
    }
}
```
### 3.3 五大基本队列
#### 3.3.1 简单模式
:::info
一个生产者，一个消费者
:::
![image.png](https://cdn.nlark.com/yuque/0/2022/png/22223333/1656862853244-e56f1b0a-1f63-4573-a23d-8f259b7dbdc3.png#clientId=u0fc8191a-cfe6-4&crop=0&crop=0&crop=1&crop=1&from=paste&height=181&id=ua0e2bb1b&margin=%5Bobject%20Object%5D&name=image.png&originHeight=181&originWidth=214&originalType=binary&ratio=1&rotation=0&showTitle=false&size=14783&status=done&style=none&taskId=ub2adf244-0137-47ff-a75d-1dff964028c&title=&width=214)
> **生产者**

```java
public class Send {
    private final static String QUEUE_NAME = "hello_word";

    public static void main(String[] args) throws Exception {
        Connection connection = RabbitMqUtils.getConnection();
        Channel channel = connection.createChannel();
        /*
         * 生成一个队列
         * 1.队列名称
         * 2.队列里面的消息是否持久化 默认消息存储在内存中
         * 3.该队列是否只供一个消费者进行消费 是否进行共享 true 可以多个消费者消费
         * 4.是否自动删除 最后一个消费者端开连接以后 该队列是否自动删除 true 自动删除
         * 5.其他参数
         */
        channel.queueDeclare(QUEUE_NAME, false, false, false, null);

        String message = "Hello Word";
        /*
         * 发送一个消息
         * 1.发送到那个交换机
         * 2.路由的 key 是哪个
         * 3.其他的参数信息
         * 4.发送消息的消息体
         */
        channel.basicPublish("", QUEUE_NAME, null, message.getBytes());
        System.out.println("消息发送完毕");

        // 接口会自动关闭，可以不写
        channel.close();
        connection.close();
    }
}
```
> **消费者**

```java
public class Receive {
    private final static String QUEUE_NAME = "hello_word";

    public static void main(String[] args) throws Exception {
        Connection connection = RabbitMqUtils.getConnection();
        Channel channel = connection.createChannel();
        System.out.println("等待接收消息......");

        // 成功回调函数
        DeliverCallback deliverCallback = (consumerTag, delivery) -> {
            String message = new String(delivery.getBody());
            System.out.println(message + "接收成功");
        };

        // 失败回调函数
        CancelCallback cancelCallback = consumerTag -> {
            System.out.println("消息被中断");
        };

        /*
         * 消费者接收消息
         * 1.消费哪个队列
         * 2.消费成功之后是否要自动应答 true 代表自动应答 false 手动应答
         * 3.消费者成功的回调
         * 4.消费者失败的回调
         */
        channel.basicConsume(QUEUE_NAME, true, deliverCallback, cancelCallback);
    }
}
```
#### 3.3.2 工作模式
:::info
一个生产者，多个消费者
:::
![image.png](https://cdn.nlark.com/yuque/0/2022/png/22223333/1656862874310-825bf608-c249-403d-b6cf-09c8b41ba7e7.png#clientId=u0fc8191a-cfe6-4&crop=0&crop=0&crop=1&crop=1&from=paste&height=179&id=u172ace80&margin=%5Bobject%20Object%5D&name=image.png&originHeight=179&originWidth=217&originalType=binary&ratio=1&rotation=0&showTitle=false&size=15623&status=done&style=none&taskId=u574dcf01-0ba2-4f7f-9fc9-cefaa950e58&title=&width=217)
> **两个消费者**

```java
public class Receive01 {
    private final static String QUEUE_NAME = "hello_word";

    public static void main(String[] args) throws Exception {
        Connection connection = RabbitMqUtils.getConnection();
        Channel channel = connection.createChannel();
        System.out.println("R1 等待接收消息......");

        // 成功回调函数
        DeliverCallback deliverCallback = (consumerTag, delivery) -> {
            String message = new String(delivery.getBody());
            System.out.println(message + "接收成功");
        };

        // 失败回调函数
        CancelCallback cancelCallback = consumerTag -> {
            System.out.println("消息被中断");
        };

        /*
         * 消费者接收消息
         * 1.消费哪个队列
         * 2.消费成功之后是否要自动应答 true 代表自动应答 false 手动应答
         * 3.消费者成功的回调
         * 4.消费者失败的回调
         */
        channel.basicConsume(QUEUE_NAME, true, deliverCallback, cancelCallback);
    }
}
```
```java
public class Receive02 {
    private final static String QUEUE_NAME = "hello_word";

    public static void main(String[] args) throws Exception {
        Connection connection = RabbitMqUtils.getConnection();
        Channel channel = connection.createChannel();
        System.out.println("R2 等待接收消息......");

        // 成功回调函数
        DeliverCallback deliverCallback = (consumerTag, delivery) -> {
            String message = new String(delivery.getBody());
            System.out.println(message + "接收成功");
        };

        // 失败回调函数
        CancelCallback cancelCallback = consumerTag -> {
            System.out.println("消息被中断");
        };

        /*
         * 消费者接收消息
         * 1.消费哪个队列
         * 2.消费成功之后是否要自动应答 true 代表自动应答 false 手动应答
         * 3.消费者成功的回调
         * 4.消费者失败的回调
         */
        channel.basicConsume(QUEUE_NAME, true, deliverCallback, cancelCallback);
    }
}
```
> **消息生产者**

```java
public class Send01 {
    private static final String QUEUE_NAME = "hello_word";
    
    public static void main(String[] args) throws Exception {
        try(Channel channel = RabbitMqUtils.getConnection().createChannel();) {
            channel.queueDeclare(QUEUE_NAME, false, false, false, null);
            Scanner scanner = new Scanner(System.in);
            while (scanner.hasNext()){
                String message = scanner.next();
                channel.basicPublish("", QUEUE_NAME, null, message.getBytes());
                System.out.println("发送消息完成:" + message);
            }
        }
    }
}
```
> **结果 - 消息轮询接收，且不重复**

![image.png](https://cdn.nlark.com/yuque/0/2022/png/22223333/1656863974116-6c9a059b-5e8f-4009-891a-9a3bb1fa1879.png#clientId=u0fc8191a-cfe6-4&crop=0&crop=0&crop=1&crop=1&from=paste&height=315&id=ua573c5de&margin=%5Bobject%20Object%5D&name=image.png&originHeight=315&originWidth=471&originalType=binary&ratio=1&rotation=0&showTitle=false&size=30091&status=done&style=none&taskId=u10894f85-fabd-4e75-9cc1-34b141a31f4&title=&width=471)
![image.png](https://cdn.nlark.com/yuque/0/2022/png/22223333/1656863998756-d360e359-410d-4d30-b7bc-881f0671ea28.png#clientId=u0fc8191a-cfe6-4&crop=0&crop=0&crop=1&crop=1&from=paste&height=242&id=u0972d746&margin=%5Bobject%20Object%5D&name=image.png&originHeight=242&originWidth=307&originalType=binary&ratio=1&rotation=0&showTitle=false&size=18344&status=done&style=none&taskId=ua1aac3ba-eed1-4dfa-9165-ac2bcc7b8fe&title=&width=307)![image.png](https://cdn.nlark.com/yuque/0/2022/png/22223333/1656864016226-218f00c4-5bdb-42dc-bf20-a7dda651e069.png#clientId=u0fc8191a-cfe6-4&crop=0&crop=0&crop=1&crop=1&from=paste&height=233&id=uebbd2bfc&margin=%5Bobject%20Object%5D&name=image.png&originHeight=233&originWidth=293&originalType=binary&ratio=1&rotation=0&showTitle=false&size=17885&status=done&style=none&taskId=u1447f748-fba8-4cff-bf85-f10967c5795&title=&width=293)
