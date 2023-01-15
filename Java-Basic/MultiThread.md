# 多线程

[TOC]

## 基本定义

### Process

进程是执行程序的依次执行过程，他是一个动态的概念。是系统资源分配的单位。

通常在一个进程中可以包含若干个线程，当然一个进程中至少有一个线程，不然没有存在的意义。线程是CPU调度和执行的单位

注意很多线程是模拟出来的，真正的多线程是指有多个cpu，即多核，如服务器。如果是模拟出来的多线程，即在一个cpu的情况下，在同一个时间点，cpu只能执行一个代码，因为切换的很快，所以就有同时执行的错局

## 线程的创建

### Thead:继承

重写run方法

调用的时候用start(),会开辟子线程

如果调用run()则是主线程来执行run方法

不建议使用：OOP单继承局限性

### Runnable：实现接口

类要继承Runnable然后重写run()方法

使用时需要借助Thread，像构造器传一个Runnable对象

```java
MyThread myThread = new MyThread();
Thread thread = new Thread(myThread);
thread.start();
```

相较Thread更加灵活



### Callable：实现Callable，现阶段了解

```java
//创建Callable对象
Callable<Boolean> c1 = new Callable<Boolean>() {
    @Override
    public Boolean call() throws Exception {
        return true;
    }
};

Callable<Boolean> c2 = new Callable<Boolean>() {
    @Override
    public Boolean call() throws Exception {
        return true;
    }
};
//创建执行服务
ExecutorService ser = Executors.newFixedThreadPool(2);

//提交执行
Future<Boolean> s1 = ser.submit(c1);
Future<Boolean> s2 = ser.submit(c2);

//获取结果
Boolean g1 = s1.get();
Boolean g2 = s2.get();

//关闭服务
ser.shutdownNow();
```

好处： 可以定义返回值， 可以抛出异常

### 扩展：静态代理

```java
public class StaticAgency {
    public static void main(String[] args) {
        You you = new You();
        MarryCompany marryCompany = new MarryCompany(you);
        marryCompany.happyMarry();
    }
}

interface Marry{
    void happyMarry();
}

class You implements Marry{

    @Override
    public void happyMarry() {
        System.out.println("我结婚了");
    }
}

class MarryCompany implements Marry{

    private Marry customer;

    public MarryCompany(Marry customer) {
        this.customer = customer;
    }

    @Override
    public void happyMarry() {
        before();
        customer.happyMarry();
        after();
    }

    private void before(){
        System.out.println("布置现场");
    }

    private void after(){
        System.out.println("收钱");
    }
}
```

真实对象和代理对象都要实现同一个接口

代理对象要代理真实角色

#### 好处

代理对象可以做很多真实对象做不了的事情

真实对象专注做自己的事情