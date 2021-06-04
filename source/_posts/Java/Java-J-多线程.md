---
title: Java 多线程
cover: https://cdn.jsdelivr.net/gh/halo-blog/cdn-blog-img-b@master/img/Java.png
tags:
  - Java
categories:
  - Java
date: 2021-5-31 16:16:54
---

参考：《Java 多线程与 Socket 实战微服务框架》

## 多线程的概念

### 进程与线程

现在的操作系统都是多任务操作系统。多任务操作系统允许多个进程在同一个 CPU 上运行。进程也是操作系统进行资源分配的最小单位，每个进程都有独立的代码和数据空间，称为<font color="#ea66a6">进程的上下文</font>。CPU 从一个进程切换到另一个进程所做的动作被称为<font color="#ea66a6">上下文切换</font>。操作系统通过频繁的上下文切换来让这些进程看起来像在同时运行一样。

进程的运行需要较多的资源，因此，操作系统能够同时运行的进程数量是有限的。进程间的切换和通信也存在较大的开销。为了能并行执行更多的任务，提升系统的效率，才引入了线程的概念。线程间的切换开销比进程间的切换开销小得多。

线程是 CPU 调度的最小单位，它是进程的一部分，只能由进程创建。一个可以进程拥有多个线程，这线线程共享进程的资源和程序代码。也就是说，一个 Java 进程（JVM 进程）至少有一个线程（称为主线程），对应到代码中就是 `main` 方法所在的线程。通过主线程就可以创建更多线程。此外，线程又分为用户线程和守护线程。两者的区别在于，后者会随主线程结束而结束。一旦所有的用户线程都结束，程序就会停止，所有守护线程也随之终止。

受操作系统的限制，每个操作系统用户能够同时运行的进程数是有限的，每个进程能够开启的线程数也有上限。在同一个 CPU 核心上，多线程以抢占或主动让出时间片的形式轮流执行。基于同一段代码可以创建多个线程，这些线程共享进程的数据空间，但每个线程有独立的运行栈和程序计数器。

### 并发与并行

并发与并行是一个相对资源的概念。

+ <font color="#ad1a72">并发</font>是指两个或多个事件在同一时间间隔内发生。
+ <font color="#ad1a72">并行性</font>是指系统具有同时进行运算或操作的特性，在同一时刻能完成两种或两种以上的工作。

如果我们在同一时间，在多台计算机上同时运行多个任务，这些任务就是在“并行”地执行。同理，如果一台计算机拥有多个 CPU ，这些 CPU 分别在执行多个运算任务，尽管这个计算机的其他资源，比如内存，是多个任务所共享的，但相对 CPU 这一资源来说，这些任务是“并行”的，这被称为“多 CPU 并行”。如果一个 CPU 拥有多个核心，并允许多个线程在不同的核心上同时执行，则称为“多核并行”。

与之相对应的是，在同一个资源上，通过某些调度算法，让用户看起来计算机是在同时执行多个任务，这就是并发。比如，在单个 CPU 上，通过一定的调度算法，“同时”执行多个任务，让这些任务看起来在一个时间段是“并行”执行的。即使在同一时刻，CPU 也只能执行这些计算任务中的一个。

### 线程状态

线程和进程一样拥有 7 种状态：新建（NEW）、就绪（RUNNABLE 或 READY）、运行（RUNNING）、阻塞（BLOCKED）、等待（WAITING）、计时等待（TIME_WAITING）、终止（TERMINATED 或 DEAD）。线程在存续过程中，其状态会在这 7 种状态之间转换。

> Java 5 以前，等待和计时等待也被归于阻塞状态，所以也有 5 种状态的说法。

+ 新建（NEW）：线程被新创建时的状态。
+ 就绪（RUNNABLE 或 READY）：线程正在参与竞争 CPU 的使用权
+ 运行（RUNNING）：线程获取到了 CPU 的使用权，正在执行。
+ 阻塞（BLOCKED）：阻塞状态指的是线程为了等待某个对象的“锁”，而暂时放弃 CPU 的使用权，且不再参与 CPU 使用权的竞争。直到条件满足（超时退出、被中断或唤醒）时，该线程才重新回到就绪状态，重新参与竞争 CPU。
+ 等待（WAITING）：线程无线等待某个对象的“锁”，或等待另一个线程结束的状态。
+ 计时等待（TIME_WAITING）：线程在一段时间内等待某个对象的“锁”，或者主动休眠，亦或者等待另一个线程结束。除非被中断，否则时间一到，（超时）线程将会自动回到就绪态。被中断的方法通常会抛出中断异常（InterruptedException），超时的方法会抛出超时异常（TimeoutException）。
+ 终止（TERMINATED 或 DEAD）：遇到以下情况，线程将终止。线程所运行的代码段被执行完毕，或执行过程中出现异常，亦或者受到外界干预而中断执行。

## Tread 线程类

### 基本用法

通过继承 Tread 类，覆盖其 `run()` 方法即可编写一个线程类：

```java
public class MyThread extends Thread {
    @Override
    public void run() {
        for (int i = 0; i < 100; i++) {
            System.out.print(i + " , ");
        }
    }

    public static void main(String[] args) {
        MyThread t = new MyThread();
        t.start();
        System.out.println("ok");
    }
}
```

> 注意：调用 `start()` 方法后，`run()` 方法中的代码并不一定立即开始执行。 `start()` 方法只是将线程变为可就绪状态，什么时候运行是由操作系统决定的。

### 常用方法介绍

Thread 类中常用的实例方法

+ `t.start()`：启动线程 t 的状态从新建转换为就绪
+ `t.checkAccess()`：检查当前线程是否有权限访问线程 t
+ `t.interrupt()`：尝试通知线程 t 中断，需要在线程的任务代码中使用
+ `t.isInterrupted()`：用于检查是否要求中断。当此方法返回 true 时，当前线程应判断是否要中断执行，如果此时不中断执行，再次调用此方法将返回 false。
+ `t.setPriority()`：设置线程 t 的优先级（1 \~ 10），值越大，得到执行的将会越高
+ `t.isDaemon()`：判断线程 t 是否为守护线程
+ `t.setDaemon(true)`：仅用于在调用 `start()` 前设置线程 t 为守护线程
+ `t.isAlive()`：判断线程 t 是否存活
+ `t.join(1000L)`：当前线程等待线程 t 终止。参数为超时时间。
+ `t.setName("Thread1")`：为线程设置一个名称

Thread 类中常用的静态方法

+ `Thread.yield()`：让当前线程让出 CPU，并转为就绪状态，重新参与 CPU 使用权的竞争。只有优先级大于等于当前进程的线程才可能获得 CPU 使用权。
+ `Thread.sleep(100L)`：让当前线程让出 CPU，睡眠（阻塞）100 毫秒，然后回到就绪状态，重新参与 CPU 使用权的竞争。
+ `Thread.currentThread()`：得到当前线程对象的引用。

### `wait` 和 `sleep` 的区别

`wait()` 方法时所有 Object 类的方法，是线程同步的重要手段之一。虽然两者都可以让程序阻塞指定的毫秒数，并且都可以通过 `interrupt()` 方法打断，但两者有很大的不同：

+ `wait()` 方法必须在 `synchronized` 同步块或方法中使用；
+ `wait()` 方法会释放由 `synchronized` 锁上的对象锁，而 `sleep()` 则不会
+ 由 `wait()` 方法形成的阻塞，可以通过针对同一对象锁的 `synchronized` 作用域用 `notify()` 或 `notifyAll()` 来唤醒，而 `sleep()` 则无法被唤醒，只能定时醒来或被 `interrupt()` 方法中断。

### `sleep` 和 `yield` 的区别

区别如下：

+ 线程执行 `sleep()` 方法后转入阻塞态，并在睡眠一段时间后自动醒来，回到就绪态。而执行 `yield()` 方法后，当前线程转入的是就绪态。
+ 当前线程执行 `sleep()` 方法后，其他线程无论优先级高低，都有机会得以运行；而执行 `yield()` 方法只会给那些具有相同或更高优先级的线程运行的机会。
+ `sleep()` 方法需要声明抛出 `InterruptedException`，而 `yield()` 方法没有声明任何异常。
+ `sleep()` 方法比 `yield()` 方法具有更好的可移植性（与操作系统的 CPU 调度有关）。如果在循环中使用 `yield()` 方法，则在 Linux 上容易导致“死循环”——当前线程在 `yield()` 后总是又立即抢占到 CPU，导致其他线程得不到执行。

## Runnable 接口

处理继承 Thread 类外，还可以通过实现 Runnable 接口来编写线程代码。实践经验告诉我们，尽量使用接口而不是继承来进行开发。使用 Runnable 接口可以将线程对象本身与线程所要执行的代码分离，同时避免 Java 的单继承限制。而且线程池也只能接受 Runnable 或 Callable 接口类型的对象作为任务。因此，建议使用 Runnable 或 Callable 接口的形式开发多线程。

下面是 Runnable 接口使用的示例：

```java
static class InterruptThread implements Runnable {
    @Override
    public void run() {
        try {
            Thread.sleep(2000);
        } catch (InterruptedException e) {
            System.out.println("线程处于阻塞状态时，中断线程，就会抛出异常。");
            e.printStackTrace();
        }

    }
}

@Test
void TestInterrupt() {
    for (int i = 0; i < 5; i++) {
        Thread thread = new Thread(new InterruptThread());
        thread.start();
        thread.interrupt();
    }
}
```

## 线程池

线程的创建和销毁会消耗资源。在大量并发的情况下，频繁地创建和销毁线程会严重影响系统的性能。因此，通常需要预先创建多个线程，并集中管理起来，形成一个线程池。在需要用到线程时，从线程池中拿出一个线程直接使用，执行完毕时再放回线程池。

### Executors

从 Java 5 开始，可以使用 `java.util.concurrent.Executors` 类来创建线程池：

（1）创建固定大小的线程池 `Executors.newFixedThreadPool(20)`

上面的代码创建了一个有着 20 个线程的固定大小线程池。又新任务时，如果池中没有空闲池可用，就加入队列等待。队列大小的上限是 `Integer.MAX_VALUE`。其优点时避免了线程创建和销毁的开销；缺点是池大小的配置容易过高或过低。无论过高或过低都会影响性能。如果希望自定义队列大小，则使用 [ThreadPoolExecutor](#ThreadPoolExecutor)

（2）创建可变大小的线程池 `Executors.newCachedThreadPool()`

上面代码创建了一个可缓存空闲线程 60 秒的线程池。有新任务时，如果池中没有空闲线程可用，则创建一个新线程并添加到池中，池的大小上限是 `Integer.MAX_VALUE`。超过 60 秒未被使用的线程将被销毁。其优点是能按需创建线程，并尽可能地回收或复用它们。该方法适用于执行生存期较短的任务。其缺点是池大小的上限太大，“洪峰”来临时容易在短期内创建并持有大量线程。如果希望自定义大小和线程的最大空闲时长，则使用 [ThreadPoolExecutor](#ThreadPoolExecutor)

（3）创建一个只有一个线程的线程池 `Executors.newSingleThreadExecutor()`

该方法等同于 `Executors.newFixedThreadPool(1)`

（4）创建一个支持定时或周期性任务执行的线程池 `Executors.newScheduledThreadPool(2)`

### ExecutorsService

通过 Executors 创建的线程池都实现了 ExecutorsService 接口，调用它的 `execute` 或 `submit` 方法即可向线程池提交任务。下面介绍了 ExecutorsService 接口的主要方法。

+ `shutdown()`：异步关闭线程池。调用后将不再向线程池提交新任务，但允许继续执行已提交到线程池的任务。调用后立即返回，不会阻塞当前线程。
+ `shutdownNow()`：立即异步关闭线程池

### ThreadPoolExecutor


### 基本用法

## Callable 与 Future

## 线程安全与效率

### 什么是线程安全

### 线程同步

### 饥饿与公平

### 锁与死锁

### 线程中断

## volatile 关键字

## synchronized 关键字

## wait notify notifyAll

## CAS 操作

## atomic 包

## Lock 自旋锁

## Condition 条件变量

## 线程安全容器

## ThreadLocal 类

## CountDownLatch 计数器

## CyclicBarrier 栅栏

## Semaphore 信号量

## fork join 框架

