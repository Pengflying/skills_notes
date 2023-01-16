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



## 线程状态

- new
- 就绪状态
- 运行状态
- 阻塞状态
- dead



### 停止线程

1. 建议线程正常停止（次数）
2. 标志位
3. 不要使用stop和destroy(已废弃或不推荐使用)



### 线程休眠

sleap(毫秒)

sleep时间到了之后，线程进入就绪状态

sleep可以模拟网络延迟，倒计时等

每一个对象都有一把锁，sleep不会释放锁



### 线程礼让

yield

礼让线程，让当前正在执行的线程暂停，但不阻塞

将线程从运行状态转为就绪状态

**让cpu重新调度，礼让不一定成功，看cpu性情**



### 合并线程

Join

合并线程，待此线程执行完成，再执行当前线程后续代码



### 线程状态观测

#### Thread.State （枚举值）

getState()调用



NEW尚未启动

RUNNABLE在虚拟机中执行

BLOCKED被阻塞等待监视器锁定的线程处于此状态

WAITING正在等待另一个线程执行特定动作的线程处于此状态

TIMED_WAITING正在等待另一个线程执行动作达到指定等待时间的线程处于此状态

TERMINATED一推出的线程处于此状态



***注意：死亡后的线程不能在启动***



### 线程的优先级

Java提供一个线程调度器来监控程序中启动后进入就绪状态的所有线程，线程调度器按照优先级决定因该调度哪个线程来执行。

优先级数字1~10，数字越大越优先

调用方法getPriority() and setPriority(int)

但是优先级也是一个参考，并不能保证一定。

优先级低只是意味着获得调度的概率低，并不是优先级低就不会调用了。这都是看cpu的调度。



### 守护线程

线程分为**用户线程**和**守护线程**

虚拟机必须确保**用户**线程执行完毕

虚拟机不用等待**守护**线程执行完毕(主线程结束，守护线程自动结束)

如：后台记录操作日志，监控内存，垃圾回收等待。。

setDaemon(true);



### 线程同步

一种等待机制，多个对象的线程进入这个对象的等待池形成队列，等待前面的线程使用完毕，下一个线程再使用。

多个线程操作同一个资源

并发：同一个对象被多个线程同时操作

#### 锁机制synchronized

存在问题

- 一个线程持有锁会导致其他所有需要此锁的线程挂起。
- 在多个线程竞争下，加锁，释放锁会导致比较多的上下文切换和调度延时，引起性能问题。
- 如果一个优先级高的线程等待一个优先级低的线程释放锁会导致优先级倒置，引起性能问题。

#### 同步方法

```java
public synchronized void methodName(){};
```

控制对象的访问，每个对象对应一把锁（this），每个synchronized方法都必须获得调用该方法的对象的锁才能执行。否则线程会阻塞，方法一旦执行，就独占该锁，直到该方法返回才释放锁，后面被阻塞的线程才能获得这个锁，继续执行

缺点：若把一个大的方法声明为synchronized将会影响效率

修改代码才需要加锁，查询类不需要。



#### 同步块

```java
public void methodName(){
        synchronized (obj){
            //内容
        }
    };
```

obj被同步监视器，只要是对象即可（一般锁资源对象，变化的量，例如账户对象）

同步监视器的执行过程

- 第一个线程访问，锁定同步监视器，执行其中代码。
- 第二个线程访问，发现obj对锁定，无法访问
- 第一个访问完毕，解锁obj
- 第二个线程访问，发现没有锁，然后锁定访问



补充：

JUC的CopyOnWriteArrayList是线程安全的list



#### 死锁

两个线程各自占有一些共享资源，并且互相等待其他线程占有的资源才能运行，而导致两个或者多个线程都在等待对方释放资源，都停止执行的情形，某一个同步代码快同时拥有“两个以上对象的锁”时，就可能会发生“死锁问题”



产生死锁的四个必要条件

1. 互斥条件：一个资源每次只能被一个进程使用
2. 请求与保持条件：一个进程因请求资源而阻塞时，对以获得的资源保持不放。
3. 不剥夺条件：进程已获得的资源，在未使用完之前，不能强行剥夺
4. 循环等待条件：若干进程之间形成一种头尾相接的循环等待资源关系 



#### Lock锁

显示定义同步锁

（Lock接口，ReentranLock可重入锁）



#### Lock和synchronized比较

Lock是显示锁（手动开启和关闭锁，别忘记关锁）synchronized是隐式锁，除了作用域自动释放

Lock只有代码块锁，synchronized有代码块锁和方法锁

使用Lock锁，JVM将花费较少的时间来调度线程，性能更好。并且具有更好的扩展性（提供更多的子类）

优先使用顺序：

Lock > 同步代码块 > 同步方法（在方法体之外）



#### 线程协作

##### 线程通信：均是Object类的方法，都只能在同步方法或者同步代码块中使用，否则会抛出异常

wait():表示线程一直等待，直到其他线程通知，与sleep不同，**会释放锁**

wait(long timeout)

notify(): 唤醒一个处于等待状态的线程

notifyAll(): 唤醒同一个对象上所有调用wait()方法的线程， 优先级别高的线程优先调度

##### 生产者消费者模式

1. 生产者-缓存区-消费者

2. 信号灯法（标志位）



#### 线程池

背景：经常创建和销毁、使用量特别大的资源，比如并发情况下的线程，对性能影响很大

思路：提前创建好多个线程，放入线程池，使用时直接获取，使用完放回池中。可以避免频繁创建销毁、实现重复利用。类似生活中的公共交通工具

好处

- 提高响应速度（减少了创建新线程的时间）
- 降低资源消耗（重复利用线程池中线程，不需要每次都创建）
- 便于线程管理
  - corePoolSize核心池的大小
  - maximumPoolSize最大线程数
  - keepAliveTime线程没有任务时最多保持多长时间后会终止

ExecutorService and Executors

ExecutorService是线程池接口，常见子类是ThreadPoolExecutor

方法：

void execute(Runnable command):执行任务、命令，没有返回值

Future\<T> submit(Callable\<T> task)执行任务，有返回值

void shutdown(): 关闭连接池



Executors:工具类、线程池的工厂类，用于创建并返回不同类型的线程池。 

参考Callable





