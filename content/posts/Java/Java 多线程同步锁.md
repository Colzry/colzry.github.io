---
title: "Java多线程同步锁"
description: "Java多线程同步锁"
keywords: "Java"

date: 2023-06-16T16:41:30+08:00
lastmod: 2023-06-16T16:41:30+08:00

categories:
  - Java
tags:
  - Java
---


> > > 同步监视器必须为**唯一的对象**
>  
> 例如：`this`, `*.class`


若使用`*.class`时，报以下错误

![](https://picture-czy.oss-cn-beijing.aliyuncs.com/img/20210516233855.png#crop=0&crop=0&crop=1&crop=1&id=ElYrK&originHeight=222&originWidth=1610&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

则原因为：
线程操作的`wait()`、`notify()`、`notifyAll()`方法只能在同步控制方法或同步控制块内调用。如果在非同步控制方法或控制块里调用，程序能通过编译，但运行的时候，将得到 `java.lang.IllegalMonitorStateException` 异常，并伴随着一些含糊信息，比如 `current thread is not owner(当前线程不是拥有者)`。其实异常的含义是 调用`wait()`、`notify()`、`notifyAll()`的任务在调用这些方法前必须 ‘拥有’（获取）对象的锁。

解决方法：
在`wait()`、`notify()`、`notifyAll()`方法调用是加上调用的对象，例如：`*.class.wait();`

### 具体实现
> 使用两个线程间隔递增的打印0到100


1. 使用`*.class`同步监视器
```java
public class demo implements Runnable{

    public static int number = 0;

    @Override
    public void run() {
        while (true) {
            synchronized (demo.class) {
                demo.class.notify();
                if (number <= 100) {
                    try {
                        Thread.sleep(100);
                    } catch (InterruptedException e) {
                        e.printStackTrace();
                    }
                    System.out.println(Thread.currentThread().getName() + " : " + number++);
                    try {
                        demo.class.wait();
                    } catch (InterruptedException e) {
                        e.printStackTrace();
                    }
                } else { System.exit(0); }

            }
        }
    }

    public static void main(String[] args) {
        demo demo = new demo();
        Thread thread1 = new Thread(demo);
        Thread thread2 = new Thread(demo);

        thread1.setName("线程1");
        thread2.setName("线程2");

        thread1.start();
        thread2.start();
    }
}
```

2. 使用`this`同步监视器
```java
public class demo implements Runnable{

    public static int number = 0;

    @Override
    public void run() {
        while (true) {
            synchronized (this) {
                this.notify();
                if (number <= 100) {
                    try {
                        Thread.sleep(100);
                    } catch (InterruptedException e) {
                        e.printStackTrace();
                    }
                    System.out.println(Thread.currentThread().getName() + " : " + number++);
                    try {
                        this.wait();
                    } catch (InterruptedException e) {
                        e.printStackTrace();
                    }
                } else { System.exit(0); }

            }
        }
    }

    public static void main(String[] args) {
        demo demo = new demo();
        Thread thread1 = new Thread(demo);
        Thread thread2 = new Thread(demo);

        thread1.setName("线程1");
        thread2.setName("线程2");

        thread1.start();
        thread2.start();
    }
}
```
